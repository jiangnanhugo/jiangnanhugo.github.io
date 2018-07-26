---
layout: "post"
title: "confidence measure in automatic speech recognition"
date: "2018-01-11 12:18"
comments: true
---

### What is "confidence measures"?
evaluate reliability of recognition results.

### Why measure confidence or reliability?
ambient noises, speaker variations, channel distortions and other mismatches.

###  Major methods in category?
1. combination of predictor features;
  - log-likelihoods,
  - word duration
  - word graph density
  - 
2. posterior probability: estimate the probability of the sequence given the acoustic observations.
3. hypothesis testing: likelihood ratio = (word likelihood score) / (background model + competing model)
4. utterance verification.








### Specific Name
- *non-keyword rejection* or *keyword recognition*: detect if the input speech does not contain any of the words in the recognizer vocabulary set.
-


## Related Papers
1. Out-Of-Vocabulary Detection and Confidence Measures for Speech Recognition Using Phone Models.
2. Confidence Estimation for Machine Translation
