---
layout: "post"
title: "Hash functions"
date: "2020-05-10 21:45"
image: '/images/hash.png'
---

## uniform hash function

for all $$i,\ell$$,
$$
Pr[h(i)=\ell]=\frac{1}{m}
$$

## 2-universal hash function
for all $$i,j$$,
$$
Pr[h(i)=h(j)]=\frac{1}{m}
$$


## pairwise independent hash function
for all $$i_1,i_2,\ell_1,\ell_2$$,
$$
Pr[h(i)=\ell_1, h(j)=\ell_2]=Pr[h(i)=\ell_1]* Pr[h(j)=\ell_2]
$$

## Example
$$h(x)= [(ax+b)% p ] % m$$ is a 2-universal hash function


#### a pairwise independent hash function that is uniform over T is certainly 2-universal.
