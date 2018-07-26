---
layout: "post"
title: "theano sparse_block_dot"
date: "2016-11-08"
comments: true
---

`sparse_block_dot` is a special function in Theano. 

#### 1. Function

```python
for b in range(batch_size):
    for j in range(o.shape[1]):
        for i in range(h.shape[1]):
            o[b, j, :] += numpy.dot(h[b, i], W[iIdx[b, i], oIdx[b, j]])
```

**Image Example**
![Images](http://deeplearning.net/software/theano/_images/blocksparse.png)

**Input Parameter**
```
- W (iBlocks, oBlocks, iSize, oSize) – weight matrix
- h (batch, iWin, iSize) – input from lower layer (sparse)
- inputIdx (batch, iWin) – indexes of the input blocks
- b (oBlocks, oSize) – bias vector
- outputIdx (batch, oWin) – indexes of the output blocks
```

**Return**

```
- dot(W[i, j], h[i]) + b[j] #but b[j] is only added once
- shape: (batch, oWin, oSize)
```

#### 2. Applications
used form calculating `theano.tensor.nnet.h_softmax`;

**Codes**

```python
def h_softmax(x, batch_size, n_outputs, n_classes, n_outputs_per_class,
              W1, b1, W2, b2, target=None):
   "Two-level hierarchical softmax."
    # First softmax that computes the probabilities of belonging to each class
    class_probs = theano.tensor.nnet.softmax(tensor.dot(x, W1) + b1)
    if target is None:  # Computes the probabilites of all the outputs
        # Second softmax that computes the output probabilities
        activations = tensor.tensordot(x, W2, (1, 1)) + b2
        output_probs = theano.tensor.nnet.softmax(
            activations.reshape((-1, n_outputs_per_class)))
        output_probs = output_probs.reshape((batch_size, n_classes, -1))
        output_probs = class_probs.dimshuffle(0, 1, 'x') * output_probs
        output_probs = output_probs.reshape((batch_size, -1))
        # output_probs.shape[1] is n_classes * n_outputs_per_class, which might
        # be greater than n_outputs, so we ignore the potential irrelevant
        # outputs with the next line:
        output_probs = output_probs[:, :n_outputs]
    else:  # Computes the probabilities of the outputs specified by the targets
        target = target.flatten()
        # Classes to which belong each target
        target_classes = target // n_outputs_per_class
        # Outputs to which belong each target inside a class
        target_outputs_in_class = target % n_outputs_per_class

        # Second softmax that computes the output probabilities
        activations = sparse_block_dot(
            W2.dimshuffle('x', 0, 1, 2), x.dimshuffle(0, 'x', 1),
            tensor.zeros((batch_size, 1), dtype='int32'), b2,
            target_classes.dimshuffle(0, 'x'))

        output_probs = theano.tensor.nnet.softmax(activations.dimshuffle(0, 2))
        target_class_probs = class_probs[tensor.arange(batch_size),
                                         target_classes]
        output_probs = output_probs[tensor.arange(batch_size),
                                    target_outputs_in_class]
        output_probs = target_class_probs * output_probs
    return output_probs
```
