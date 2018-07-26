---
layout: "post"
title: "Training with Unbalanced dataset"
date: "2018-04-08 10:42"
comments: true
---

## Ensemble Methods
boosting and bagging Methods

## Focal loss method

## Hard Negative Mining
```python
loss=cross_entropy(predicted, ground_truth)
toped_loss=topk(loss,k=batch_size* ratio) # ratio=0.75 maybe
averaged_loss=average(toped_loss)
return averaged_loss
```
