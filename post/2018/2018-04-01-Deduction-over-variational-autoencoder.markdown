---
layout: "post"
title: "Deduction over Variational Autoencoder"
date: "2018-04-01 22:20"
---


<!--
### Model setups

- Encoder: $$y=Wx$$
- Decoder: $$\hat x=Uy$$
- Loss:
$$\min \ell=||x-\hat x||^2$$

### Usage

- Dimension reduction
 -->


#### 1. KL Divergence
$$\text{KL}(p||q)$$ means the information of $$q(x)$$ minus the information of $$p(x)$$, so it should be like:

$$\text{KL}(p||q)=-\sum q(x)\log q(x) +\sum p(x) \log p(x) $$


But we should bear in mind that "with respect to distribution $$p(x)$$". So the exact definition should change the first term $$\sum q(x)\log q(x)$$ to  $$\sum p(x)\log q(x)$$. So the $$\text{KL}(p\|q)$$ would be:

$$
\begin{aligned}
\text{KL}(p||q)&=-\sum p(x)\log q(x) +\sum p(x) \log p(x) \\
&=\sum p(x)\log \frac{p(x)}{q(x)} \\
&=-\sum p(x)\log \frac{q(x)}{p(x)}
\end{aligned}
$$

and its traits: 1) $$\text{KL}\ge 0$$; 2)
$$\text{KL}(p||q)\neq \text{KL}(q||p)$$. So it's not distance but divergence, since distance should be symmetric.

#### 2. Graphical Modeling
Variational technique is mostly used in Graphical model. So suppose there is $$z,x$$, $$z$$ is my hidden variables and $$x$$ is my observation. The posterior probability:

$$p(z|x)=\frac{p(x|z)p(z)}{p(x)}=\frac{p(x,z)}{p(x)}$$

computing $$p(x)$$ is complicated, we need to calculate the marginal distribution, which is:

$$p(x)=\int p(x|z)p(z)dz $$

this integral is intractable in many cases. So roughly there are two main approaches to handle this: 1). Monte Carlo sampling; 2) Variational Inference. The first approach is zero bias with high variance, while the second one has zero variance but is biased estimator.


#### 3. Variational Inference

approximate
$$p(z|x)$$
by another distribution $$q(z)$$, which suppose to be a attractable distribution. So we want to minimize the joint loss

<!-- $$\\
\-->
$$\begin{aligned}
\min \text{KL}(q(z)\|p(z|x))&=-\sum q(z)\log \frac{p(z|x)}{q(z)} \\
&=-\sum q(z)\log \frac{p(x,z)}{p(x)}\times \frac{1}{q(z)}\\
&=-\sum q(z)\log \frac{p(x,z)}{q(z)} \times \frac{1}{p(x)}\\
&=-\sum q(z)\log \frac{p(x,z)}{q(z)}-\log p(x)\\
&=-\sum q(z)\log \frac{p(x,z)}{q(z)}+\sum_z q(z)\log p(x)\end{aligned}$$
Since $$\sum_z q(z)\log p(x)=\log p(x)\sum_z q(z) =\log p(x)$$ , so we can transform the equation into this form:

$$\log p(x)=\text{KL}(q(x)\|p(x|x)+\sum q(z)\log \frac{p(x,z)}{q(z)}$$

where $$\log p(x)$$ is a constant. The $$\text{KL}$$ is the term we want to minimize in the first place, so that we can maximize the second term, which is called varitional lower bound.

$$
\ell=\sum q(z)\log \frac{p(x,z)}{q(z)}$$

as $$\ell \leq \log p(x)
$$

If we maximize the lower bound of this function, we are also maximize the original function.

#### References
- [Ali Ghodsi, Lec : Deep Learning, Variational Autoencoder, Oct 12 2017](https://www.youtube.com/watch?v=uaaqyVS9-rM)
