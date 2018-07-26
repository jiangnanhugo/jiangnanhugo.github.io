---
layout: "post"
title: "Caffe2: train models by feeding the training data and labels on the fly?"
date: "2018-04-02 17:15"
---

1. import related modules.

```python
from caffe2.python import core, brew, workspace, model_helper
from caffe2.proto import caffe2_pb2
from caffe2.python.predictor import mobile_exporter
from caffe2.python.optimizer import (build_sgd, build_adagrad, build_adam)
import time
import itertools
import numpy as np

core.GlobalInit(['caffe2', '--caffe2_log_level=0'])
```

2. create input and label:

```python
def AddInput(model, batch_size, db, db_type):
    data, label = brew.db_input(model, ["data", "label"], # attention
                                batch_size=batch_size, db=db,
                                db_type=db_type)
    return data, label
```

3. build model structure.

```python
def add_mlp_model(model, data, device_opts, sizes=[20, 30, 10]):
    with core.DeviceScope(device_opts):
        layer = data
        for i in range(len(sizes) - 2):
            layer = brew.fc(model, layer, "dense_{}".format(i),
                            dim_in=sizes[i], dim_out=sizes[i + 1],
                            weight_init=('XavierFill', {}),
                            bias_init=('ConstantFill', {}))
            layer = brew.relu(model, layer, "relu_{}".format(i))
        layer = brew.fc(model, layer, "dense_{}".format(len(sizes) - 1),
                        dim_in=sizes[len(sizes) - 2],
                        dim_out=sizes[len(sizes) - 1],
                        weight_init=('XavierFill', {}),
                        bias_init=('ConstantFill', {}))
        softmax = brew.softmax(model, layer, "softmax")
    return softmax

def add_accuracy_and_xent(model, softmax, label):
    accuracy = brew.accuracy(model, [softmax, label], 'accuracy')
    xent = model.LabelCrossEntropy([softmax, label], 'xent')
    return accuracy, xent

def add_training_operators(model, softmax, label, opt_type='adam',lr=0.0001):
    _, xent = add_accuracy_and_xent(model, softmax, label)
    loss = model.AveragedLoss(xent, 'loss')
    model.AddGradientOperators([loss])
    if opt_type == 'adam':
        build_adam(model, base_learning_rate=lr)
    elif opt_type == 'sgd':
        build_sgd(model, base_learning_rate=lr, gamma=0.9,
                  policy='step', stepsize=1)
    elif opt_type == 'adagrad':
        build_adagrad(model, base_learning_rate=lr)

def build_model(valid_file, batch_size, db_type, sizes, device_opts, gpu_id, lr, grad_type='sgd'):
    # Training model
    train_model = model_helper.ModelHelper(name="lineimage_feature_train")
    train_model.net.RunAllOnGPU(gpu_id=gpu_id, use_cudnn=True)
    train_model.param_init_net.RunAllOnGPU(gpu_id=gpu_id, use_cudnn=True)
    softmax = add_mlp_model(train_model, "data", sizes=sizes, device_opts=device_opts)
    add_training_operators(train_model, softmax, "label", grad_type, lr)

    # validing model.
    valid_model = model_helper.ModelHelper(name="lineimage_feature_valid", init_params=False)
    valid_model.net.RunAllOnGPU(gpu_id=gpu_id, use_cudnn=True)
    valid_model.param_init_net.RunAllOnGPU(gpu_id=gpu_id, use_cudnn=True)
    data, label = AddInput(valid_model, batch_size=batch_size, db=valid_file, db_type=db_type)
    softmax = add_mlp_model(valid_model, data, sizes=sizes, device_opts=device_opts)
    add_accuracy_and_xent(valid_model, softmax, label)

    return train_model, valid_model, test_model
```

4. start training your model. you need to create a `data` and `label` in the current graph `workspace` before training.

```python
def train(train_file, train_counts, valid_file, valid_counts, lr ,dumped_file,
          gpu_id, sizes, max_epoch=10, batch_size=100,
          db_type='minidb', grad_type='sgd'):
    # Initialize train_model, test_model and deploy_model
    device_opts = core.DeviceOption(caffe2_pb2.CUDA, gpu_id)
    data_reader = model_helper.ModelHelper(name='minidb_reader')
    device_opts = core.DeviceOption(caffe2_pb2.CUDA, gpu_id)
    data_reader.TensorProtosDBInput([], ['data', 'label'],
                                    batch_size=batch_size,
                                    db=train_file, db_type=db_type)
    workspace.RunNetOnce(data_reader.param_init_net)
    workspace.CreateNet(data_reader.net)

    train_model, valid_model = build_model(valid_file,batch_size=batch_size,
                                           db_type=db_type, sizes=sizes,lr=lr,
                                           device_opts=device_opts, gpu_id=gpu_id,
                                           grad_type=grad_type)
    workspace.RunNetOnce(train_model.param_init_net)
    workspace.RunNetOnce(base_model.param_init_net)
    workspace.RunNetOnce(valid_model.param_init_net)
    # creating the network
    workspace.CreateNet(train_model.net)
    workspace.CreateNet(base_model.net)
    workspace.CreateNet(valid_model.net)
    print 'max epoch', max_epoch
    for epoch in range(max_epoch):
        max_iters = int(train_counts / batch_size)
        trainloss = 0
        trainacc = 0
        start = time.time()
        for i in range(max_iters):
            workspace.RunNet(data_reader.Proto().name)
            X = workspace.FetchBlob("data")
            Y = workspace.FetchBlob("label")
            # print X
            workspace.FeedBlob('data', X, device_option=device_opts)
            workspace.FeedBlob('label', Y, device_option=device_opts)
            workspace.RunNet(base_model.net.Proto().name)
            trainloss += workspace.FetchBlob('loss')
            trainacc += workspace.FetchBlob('accuracy')

        print "Epoch: %d, train acc: %.4f%%, train loss: %.4f" %
          (epoch, float(trainacc * 100. / max_iters), float(trainloss / max_iters)),
        timed = time.time() - start

        max_iters = int(valid_counts / batch_size)
        validacc = 0
        validxent = []
        for i in range(max_iters):
            workspace.RunNet(valid_model.net.Proto().name)
            validacc += workspace.FetchBlob('accuracy')
            validxent.append(workspace.FetchBlob('xent'))

        validacc = float(validacc * 100. / max_iters)
        validxent = np.mean(np.asarray(validxent).flatten())
        print "valid acc: %.4f%%, valid loss: %.6f" % (validacc, validxent),
        print "Time:", timed

    with open(".".join([dumped_file, '.train_net.pbtxt']), 'w')as fid:
        fid.write(str(train_model.Proto()))
    with open(".".join([dumped_file, '.train_init_net.pbtxt']), 'w')as fid:
        fid.write(str(train_model.param_init_net.Proto()))

```

#### References
- [Training models without a database file](https://github.com/caffe2/caffe2/issues/1404)
