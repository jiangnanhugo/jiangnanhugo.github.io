---
layout: "post"
title: "gradient methods summary"
date: "2018-11-19 17:51"
---

### Momentum
1. $$z_k$$ is noted as momentum
2. $$w$$ is the parameters,
3. $$\nabla f(w^k)$$ is gradient.

The Momentum-based gradient descent is:
$$
z_{k+1}​=\beta z_k+\nabla f(w^k) \\
w_{​k+1}=w_{​k}-\alpha z_{​k+1}
$$

1. $$\beta=0$$, we recover the gradient descent.
2. $$\beta=0.99$$, this appears to be the boost we need.


### References
1. [Why Momentum Really Works](https://distill.pub/2017/momentum/)
