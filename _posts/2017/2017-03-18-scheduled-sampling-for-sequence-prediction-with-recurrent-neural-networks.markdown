---
layout: "post"
title: "Scheduled sampling for sequence prediction with recurrent neural networks"
date: "2017-03-18 22:32"
comments: true
---


1. traditional seq2seq model is trained by teacher-forcing, which is totally different during inference.
2. In order to optimize the test score (i.e., BLEU or word-error-rate), they propose to sampling $y_t$  during training to minimise the gap between training and testing.
3. the sampling function they propose is $\exp$ family that decreased with time, other forms of sampling strategy is not tried. (important!)


#### References
- Scheduled sampling for sequence prediction with recurrent neural networks.
