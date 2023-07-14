---
layout: "post"
title: "Hierarchical clustering"
date: "2017-04-20 11:38"
comments: true
---

#### 1. Hierarchical Clustering Approach

- A typical clustering analysis approach via partitioning data set sequentially
- Construct nested partitions layer by layer via grouping objects into a tree of clusters (without the need to know the number of clusters in advance)
- Use (generalized) distance matrix as clustering criteria.


####  2. Strategies：Agglomerative Vs. Divisive

Two sequential clustering strategies for constructing a tree of clusters

- Agglomerative: a bottom-up strategy
  - Initially each data object is in its own (atomic) cluster
  - Then merge these atomic clusters into larger and larger clusters

- Divisive: a top-down strategy
  - Initially all objects are in one single cluster
  - Then the cluster is subdivided into smaller and smaller clusters

#### 3. Hierarchical Clustering

The number of dendrograms with n: $$\text{leafs}=\frac{(2n-3)!}{[2^{(n-2)}(n-2)!]}$$

Bottom-Up (agglomerative): Starting with each item in its own cluster, find the best pair to merge into a new cluster. Repeat until all clusters are fused together.

#### 4. Compute distances between clusters

- **Single Link**: smallest distance between an element in one cluster and an element in the other. e.g.,

$$d(c_i,c_j)=\min(d(x_{ip},d_{jq}))$$


- **Complete Link**: largest distance between an element in one cluster and an element in the other, i.e.,

$$d(c_i,c_j)=\max(d(x_{ip},d_{jq}))$$


- **Average**: avg distance between elements in one cluster and elements in the other, i.e.,

$$d(c_i,c_j)=avg\{d(x_{ip},d_{jq})\}$$

#### 5. Summary

hierarchical algorithm is a sequential clustering algorithm
  - use distance matrix to construct a tree of clusters (dendrogram)
  - hierarchical representation without the need of knowing # of clusters

major weakness of agglomerative clustering methods
  - can never undo what was done previously
  - sensitive to cluster distance measures and noise/outliers
  - less efficient: $$O(n^2\log n)$$, where n is the number of total objects

#### 6. Comparison with k-means

- kmeans clustering produces a single partitioning
- hierarchical clustering can give different partitionings depending on the level-of-resolution we are looking at
- kmeans clustering needs the number of clusters to be specified
- hierarchical clustering doesn't need the number of clusters to be specified
- kmeans clustering is usually more efficient run-time wise
- hierarchical clustering can be slow (has to make several merge/split decisions)
- no clear consensus on which of the two produces better clustering

#### References
- [Practical Machine Learning with Python](https://www.youtube.com/watch?v=OGxgnH8y2NM&list=PLQVvvaa0QuDfKTOs3Keq_kaG2P55YRn5v)
- [Hierarchical clustering](https://nlp.stanford.edu/IR-book/html/htmledition/hierarchical-clustering-1.html)
