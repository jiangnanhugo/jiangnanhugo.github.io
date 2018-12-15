---
layout: "post"
title: "gradient methods summary"
date: "2018-11-19 17:51"
---

### Momentum
1. $$m_k$$ is noted as momentum, also call velocity.
2. $$w$$ is the parameters,
3. $$\nabla f(w_k)$$ is gradient.

The Momentum-based gradient descent is:


\begin{aligned}
m_k&= \beta m_{k-1} +\nabla f(w_k)  \\\\
w_k&=w_{k-1} - \alpha m_k
\end{aligned}



1. $$\beta=0$$, we recover the gradient descent.
2. $$\beta=0.99$$, this appears to be the boost we need.

### Adam

\begin{eqnarray}
m_k &= \beta_1 m_{k-1} + (1-\beta_1)\nabla f(w_k)  \\\\
v_k &= \beta_2 v_{k-1} + (1-\beta_2)(\nabla f(w_k))^2  \\\\
\hat m_k &= \frac{m_k}{1-\beta_1^t} \\\\
\hat v_k &= \frac{v_k}{1-\beta_2^t} \\\\
w_k &=w_{k-1} - \alpha \frac{\hat m_k}{\sqrt{\hat v_k} +\varepsilon}
\end{eqnarray}




### References
1. [Why Momentum Really Works](https://distill.pub/2017/momentum/)
