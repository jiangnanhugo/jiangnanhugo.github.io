---
layout: "post"
title: "Regression with the L1-Norm"
date: "2018-07-23 15:56"
comments: true
---

#### 1. The L1-norm
Use absolute error:

$$\arg\min_{w\in \mathbb{R}^d}\sum_{i=1}{n}{|w^\top x_i -y_i|}$$

as the target function. Now the model can put less focus on far-away points, which is a form of **Robust Regression**.

If we write this as matrix form, which is L1-norm:

$$\arg\min_{w\in \mathbb{R}^d}{\|x_w -y\|}_{1}$$

where $$x_w=[w^\top x_0,w^\top x_1,\cdots, ]$$.

#### 2. The non-smooth issue
The gradient does not exist at $$x=0$$ points, such that it is not a convex function at $$x=0$$ point and makes the absolute error minimization harder. One method for this case is to transform this absolute error as **linear programming**.

#### 3. The constrained problems
We can convert the L1-norm as the maximum of smooth functions:

$$\|w\|\to \max\{w_l -w\}$$

1. replace **maximum with new variable**, constrained to upper-bound max:

$$\|w\| \to \arg\min_{w\in R_l, r\in R}{r_l}, r\ge \max{w_l-w}$$

where $$r=\|w\|$$

2.  replace individual constraint with **constraint for each element** of max:

$$\|w\| \to \arg\min_{w\in R_l, r\in R}{r_l}$$

$$\text{s.t.}\prod r\ge w $$

$$ r\ge -w$$


#### References
[Mini Course 3 - Stochastic Convex Optimization Methods In Machine Learning](http://video.impa.br/index.php?page=mini-courses-svan-2016)
