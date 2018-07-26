---
layout: "post"
title: "Deduction over Variational Autoencoder"
date: "2018-04-01 22:20"
---

## 0. Autoencoder

### Model setups

- Encoder: $$y=Wx$$
- Decoder: $$\hat x=Uy$$
- Loss:
$$\min \ell=||x-\hat x||^2$$

### Usage

- Dimension reduction



## 1. Variational Autoencoder

###  KL Divergence

$$
\begin{array}{l}
KL(p||q)=-\sum p(x)\log q(x) +\sum p(x) \log p(x) \\
=p(x)\log \frac{p(x)}{q(x)}=-\sum p(x)\log \frac{q(x)}{p(x)}
\end{array}
$$

information with respect to $$p$$, and its traits: 1) $$KL>=0$$; 2)
$$KL(p||q)\neq KL(q||p)$$.

so it's not distance but divergence.

#### Graphical Model

$$p(z|x)=\frac{p(x|z)p(z)}{p(x)}=\frac{p(x,z)}{p(x)}$$

Computing $$p(x)$$ is complicated, as:
$$p(x)=\int p(x|z)p(z)dz $$
, this integral is intractable in many cases.

#### How To Calculate?

##### 1. Monte Carlo sampling

##### 2. Variational Inference

approximate
$$p(z|x)$$
by another distribution $$q(z)$$, so we want to minimize the joint loss

<!-- $$\min KL(q(x)||p(x|x))=-\sum q(x)\log \frac{p(z|x)}{q(z)}\\
=-\sum q(z)\frac{p(x,z)p(z)}{p(x)}\times \frac{1}{q(z)}\\
=-\sum q(z)\log \frac{p(x,z)}{q(z)}\times \frac{1}{p(x)}\\
=-\sum q(z)[\log \frac{p(x,z)}{q(z)}-\log p(x)]​\\
=-\sum q(z)\log \frac{p(x,z)}{q(z)}+\sum_z q(z)\log p(x)$$ -->
$$\sum_z q(z)\log p(x)=q(z)\sum_z \log p(x)=(x)$$ , so

$$\log p(x)=KL(q(x)||p(x|x)+\sum q(z)\log \frac{p(x,z)}{q(z)}$$,
the summation is a constant.

variational lower bound: $$\ell=\sum q(z)\log \frac{p(x,z)}{q(z)}$$ as $$\ell \leq \log p(x)$$.

If we maximize the lower bound of this function, we are also maximize the original function.

#### References
- [Ali Ghodsi, Lec : Deep Learning, Variational Autoencoder, Oct 12 2017](https://www.youtube.com/watch?v=uaaqyVS9-rM)
