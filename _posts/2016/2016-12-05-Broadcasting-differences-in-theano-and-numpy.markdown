---
layout: "post"
title: "Broadcasting Differences in Theano and Numpy"
date: "2016-12-05 12:54"
comments: true
---

Traditional arithmetic operations is operated over tensor with exactly compatible shape. While broadcast allows tensor (scalar, vector, matrix or high dimensional arrays) with different shape to operate arithmetic operations (add or multiply) by **replicating the smaller tensor along the lacking dimension**. Therefore, it allows a scalar (one number) may be added to a matrix (2d array), a vector (1d array) to a matrix  or a scalar to a vector.


### 1. Broadcast in Numpy

In this case, the two arrays must have exactly the same shape:

```python
>>> a=np.array([1.0,2.0,3.0])
>>> b=np.array([2.0,2.0,2.0])
>>> print a*b
    array([2., 4., 6.])
```

numpy broadcast mechanism relaxes this constraint when the arrays' shape meet certain constraints: 1) they are equal, or
2) one of them is 1.

The smaller array is `broadcast` across the larger array so that they have compatible shapes.

```python
a=np.array([1.0,2.0,3.0])
b=2
print a*b
>>> array([2., 4., 6.])
```

More detailed rules:

```
Image  (3d array): 256 x 256 x 3
Scale  (1d array):             3
Result (3d array): 256 x 256 x 3
```

```
A      (4d array):  8 x 1 x 6 x 1
B      (3d array):      7 x 1 x 5
Result (4d array):  8 x 7 x 6 x 5
```


```
A      (2d array):  5 x 4
B      (1d array):      1
Result (2d array):  5 x 4

A      (2d array):  5 x 4
B      (1d array):      4
Result (2d array):  5 x 4

A      (3d array):  15 x 3 x 5
B      (3d array):  15 x 1 x 5
Result (3d array):  15 x 3 x 5

A      (3d array):  15 x 3 x 5
B      (2d array):       3 x 5
Result (3d array):  15 x 3 x 5

A      (3d array):  15 x 3 x 5
B      (2d array):       3 x 1
Result (3d array):  15 x 3 x 5
```

### 2. Broadcast in Theano

The image examples in theano are shown below.

![Theano bcast](http://deeplearning.net/software/theano/_images/bcast.png)

where `T` and `F` stands for `True` and `False` respectively, denoting which dimension can be broadcasted.



### 3. Differences

1. numpy broadcast dynamically;
2. theano needs to knows, for any operations which supports broadcasting, which dimensions will need to be broadcasted.

### References
- [Theano broadcast Doc](http://deeplearning.net/software/theano/tutorial/broadcasting.html)
- [Numpy broadcast Doc](https://docs.scipy.org/doc/numpy-1.13.0/user/basics.broadcasting.html)
