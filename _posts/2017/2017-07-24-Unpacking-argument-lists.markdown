---
layout: "post"
title: "Unpacking Argument Lists"
date: "2017-07-24 21:55"
comments: True
---

This part is used for those who want to passing many different parameters to the models, we can pass list parameters or dict parameters to the functions by the below operations.

**Lists**: the param *sequence* should be aligned with the function parameters.

```python
param=[x_train, y_train, learning_rate, dropout_keep, other_keywords]
model.train(*param)
```

**Dict**: passing parameters using key-value dictionaries.

```python
params={'x_train': x_train,
        'y_train': y_train,
        'learning_rate': lr,
        'dropout_keep': dropout_keep,
        'other_keywords': others}
model.train(**param)
```
