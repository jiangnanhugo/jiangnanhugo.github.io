---
layout: "post"
title: "Perplexity Vs Cross-entropy"
date: "2016-06-23 20:20"
comments: true
---

## Evaluating a Language Model: Perplexity
We have a serial of $$m$$ sentences:
$$s_1,s_2,\cdots,s_m$$

We could look at the probability under our model $$\prod_{i=1}^m{p(s_i)}$$. Or more conveniently, the log probability:

$$\log \prod_{i=1}^m{p(s_i)}=\sum_{i=1}^m{\log p(s_i)}$$

where $$p(s_i)$$ is the probability of sentence $$s_i$$.

In fact, the usual evaluation measure is perplexity:

$$PPL=2^{-l}$$

$$l=\frac{1}{M}\sum_{i=1}^m{\log p(s_i)}$$

and $M$ is the total number of words in the test data.

## Cross-Entropy

Given words $$x_1,\cdots,x_t$$, a language model products the following word $$x_{t+1}$$ by modeling:

$$P(x_{t+1}=v_j|x_t\cdots,x_1)=\hat y_j^t$$

where $$v_j$$ is a word in the vocabulary.

The predicted output vector $$\hat y^t \in  \mathbb{R}^{V}$$ is a probability distribution over the vocabulary, and we optimize the cross-entropy loss:

$$\mathcal{L}^t(\theta)=CE(y^t,\hat y^t)=-\sum_{i=1}^{|V|}{y_i^t\log \hat y_i^t}$$

where $$y^t$$ is the one-hot vector corresponding to the target word. This is a point-wise loss, and we sum the cross-entropy loss across all examples in a sequence, across all sequences in the dataset in order to evaluate model performance.

## The relationship between cross-entropy and ppl

$$PP^t=\frac{1}{P(x_{t+1}^{pred}=x_{t+1}|x_t\cdots,x_1)}=\frac{1}{\sum_{j=1}^V {y_j^t\cdot \hat y_j^t}}$$

which is the inverse probability of the correct word, according to the model distribution $$P$$.

suppose $$y_i^t$$ is the only nonzero element of $$y^t$$. Then, note that:

$$CE(y^t,\hat y^t)=-\log \hat y_i^t=\log\frac{1}{\hat y_i^t}$$

$$PP(y^t,\hat y^t)=\frac{1}{\hat y_i^t}$$

Then, it follows that:

$$CE(y^t,\hat y^t)=\log PP(y^t,\hat y^t)$$

In fact, minimizing the arthemtic mean of the cross-entropy is identical to minimizing the geometric mean of the perplexity. If the model predictions are completely random, $$\mathbb{E}[\hat y_i^t]=\frac{1}{V}$$, and the expected cross-entropies are $$\log V$$, ($$\log 10000\approx 9.21$$)
