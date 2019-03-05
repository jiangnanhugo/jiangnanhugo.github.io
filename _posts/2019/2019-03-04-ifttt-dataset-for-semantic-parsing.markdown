---
layout: "post"
title: "IFTTT dataset for semantic parsing"
date: "2019-03-04 20:11"
comments: true
---

### Format of IFTTT

```python
If trigger_channel.trigger_function
Then action_channel.action_function
```
This can be viewed as simple logic form. The problem of this dataset lies in the natural language description, which is not always contain words or sub-sequence that is corrected with "trigger/action".

### two versions of dataset
dataset from [1] contains several arguments and dataset from [2] with only limited arguments. [1] is pretty challenging to work on, as the pattern of function and arguments are really flexible. [2] is relatively easier to start with, and one trivial case is that author report ensemble model performance, denoting there might be issue with training variance of single model.


### References
[1] Confidence Modeling for Neural Semantic Parsing.
[2] Latent Attention For If-Then Program Synthesis.
