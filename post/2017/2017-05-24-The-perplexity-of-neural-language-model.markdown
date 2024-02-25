---
layout: "post"
title: "The Perplexity of Neural Language Model"
date: "2017-05-24 16:24"
comments: true
---



Given a list of word vectors: $$x_1,\cdots,x_{t-1},x_t,x_{t+1},\cdots,x_T $$

$$\begin{array}{l}
\hat y_t &=\text{softmax}(W h_t) \\
\hat p(x_{t+1}=v_j|x_t,\cdots,x_1) &=\hat{y}_{t,j}
\end{array}
$$

$$h_t$$ is the hidden state output at time step $$t$$. $$\hat{y}\in\mathbb{R}^{V}$$ is a probability distribution over the vocabulary, same cross entropy loss function but predicting words instead of classes

$$J^{(t)}(\theta)=-\sum_{j=1}^{|V|}{y_{t,j}\log \hat{y}_{t,j}}$$

Evaluation could just be negative of average log probability over dataset of size (number of words) T:

$$\ell=-\frac{1}{T}\sum_{t=1}^T\sum_{j=1}^{|V|}{y_{t,j}\log \hat{y}_{t,j}}$$

Perplexity: $$\exp(\ell)$$

Lower is better !

### References
- [cs224n: Natural Language Processing with Deep Learning - Lecture 7](http://web.stanford.edu/class/cs224n/lectures/cs224n-2017-lecture8.pdf)

- [CS 498: Introduction to Natural Language Processing](https://courses.engr.illinois.edu/cs498jh/Slides/Lecture04.pdf)
