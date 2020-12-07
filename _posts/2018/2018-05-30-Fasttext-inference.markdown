---
layout: "post"
title: "FastText Inference"
date: "2018-05-30 22:45"
---
This part of codes is used for those want to apply quantization to Fasttext bin file, but want to dump much smaller bin file size. So I remove the output softmax part in the bin file, which is used for calculate the `similarity` task. The only function preserved `print-word-vectors` can print the quantized word vectors for the specified word. If you do not need quantization, this part of codes is not useful for you.



1. train word vectors using FastText.

```bash
./fasttext supervised -input "${DATADIR}/dbpedia.train"
                      -output "${RESULTDIR}/dbpedia" -dim 10
                      -lr 0.1 -wordNgrams 2 -minCount 1
                      -bucket 10000000 -epoch 5 -thread 4
```

2. One issue of using Fasttext tools is that the dumped model bins is relatively large. So, it needs quantization.

```bash
./fasttext quantize -output "${RESULTDIR}/dbpedia"
                   -input "${DATADIR}/dbpedia.train"
                   -qnorm -retrain -epoch 1
                   -cutoff 100000
```

3. After the model is quantized, the output prediction part of Fasttext is also dumped, which can be removed from the model bin.

4. `cat ./change.sh` to get slimed bin dumpfile:

```bash
./fasttext -slim -output "${RESULTDIR}/dbpedia"
                 -input "${DATADIR}/dbpedia.train"
```

generated slimed dumpfile and remove `output_` and `qoutput_`


5. it will generate **`fasttext_inference`**, and only function **`print-word-vectors`** can be called.

6. you can visit **Codes**: [Fasttext_Inference](https://github.com/jiangnanhugo/fasttext_inference)

### References:
- FastText.zip: compressing text classification models
- Product quantization for nearest neighbor search
