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
m_k &= \beta m_{k-1} +\nabla f(w_k)  \\\\\\
w_k &=w_{k-1} - \alpha m_k
\end{aligned}



1. $$\beta=0$$, we recover the gradient descent.
2. $$\beta=0.99$$, this appears to be the boost we need.

### Adam

\begin{aligned}
m_k &= \beta_1 m_{k-1} + (1-\beta_1)\nabla f(w_k)  \\\\\\
v_k &= \beta_2 v_{k-1} + (1-\beta_2)(\nabla f(w_k))^2  \\\\\\
\hat m_k &= \frac{m_k}{1-\beta_1^t} \\\\\\
\hat v_k &= \frac{v_k}{1-\beta_2^t} \\\\\\
w_k &=w_{k-1} - \alpha \frac{\hat m_k}{\sqrt{\hat v_k} +\varepsilon}
\end{aligned}

```c++
__global__
void adam_update_kernel(float *w, float *g, float *m, float *v,
        int N, float beta1, float beta2,
        float correction, float eps, const float lr) {
    CUDA_KERNEL_LOOP(i, N) {
        float gi = g[i];
        float mi = m[i] = m[i] * beta1 + gi * (1 - beta1);
        float vi = v[i] = v[i] * beta2 + gi * gi * (1 - beta2);
        float ng = lr * correction * mi / (sqrt(vi) + eps);
        w[i] += ng;
    }
}

void adam_update(float *w, float *g, float *m, float *v, int N, float beta1,
        float beta2, float correction, float eps, const float lr) {
    const dim3 blockSize(CUDA_NUM_THREADS, 1, 1);
    const dim3 gridSize(GET_BLOCKS(N), 1, 1);
    adam_update_kernel<<<gridSize, blockSize>>>(w, g, m, v, N, beta1, beta2,
            correction, eps, lr);
}

void Optimzer::Adam(float *w, float *g, float *m, float *v, int size) {
    const float eps = 1e-8, beta1 = 0.9, beta2 = 0.999;
    const float correction = sqrt(1. - pow(beta2, _t)) / (1. - pow(beta1, _t));
    adam_update(w, g, m, v, size, beta1, beta2, correction, eps, _lr);
}
```




### References
1. [Why Momentum Really Works](https://distill.pub/2017/momentum/)
