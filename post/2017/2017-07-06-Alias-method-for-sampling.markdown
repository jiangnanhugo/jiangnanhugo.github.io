---
layout: "post"
title: "Alias method for sampling"
date: "2017-07-06 12:15"
comments: true
---

Sampling from multiple discrete items with diverse probability is relatively **slow**. When applying the noise contrastive estimation method, the intuitive sampling method becomes a bottleneck for the model's training. So we apply the alias method for generating samples from the given noise distribution.



#### Generate Samples
if you want to generate many samples from the distribution, and don't want specific tokens to be appear.
1. generate 1 sample, check whether this sample is not equal to your specific token. then loop though this step, until you get as much samples as you want. (slow)
2. generate 3 times more samples and remove the specific tokens in this list. (relatively faster)

#### References
1. [Sampling from Many Discrete Outcomes: the Alias Method](http://cgi.cs.mcgill.ca/~enewel3/posts/alias-method/index.html)
2. [Python implementation of Vose's alias method](https://github.com/asmith26/Vose-Alias-Method)
3. [language model with NCE via alias method](https://github.com/jiangnanhugo/lmkit/tree/master/nce)
4. [Darts, Dice, and Coins: Sampling from a Discrete Distribution](http://www.keithschwarz.com/darts-dice-coins/)
5. [The Alias Method: Efficient Sampling with Many Discrete Outcomes](https://hips.seas.harvard.edu/blog/2013/03/03/the-alias-method-efficient-sampling-with-many-discrete-outcomes/)
