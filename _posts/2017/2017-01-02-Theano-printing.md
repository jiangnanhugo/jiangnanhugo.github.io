---
layout: post
title: "Theano Printing"
description: "daily post"
comments: true
keywords: "theano, python"
---


To visualize the internal relation graph of theano variables.

##### Installing

1. `conda install pydot graphviz`


2. add graphviz path `D:\Anaconda\Library\bin\graphviz`to system `PATH` [windows version]

or:

1. download installer from `http://www.graphviz.org/Download_windows.php`.
2. `conda install pydot graphviz`
3. add graphviz bin path  `C:\Program Files (x86)\Graphviz2.38\bin` to system PATH [windows version]

#### Application

```python
>>> import theano
>>> import theano.tensor as T
>>> x=T.matrix('x')
>>> y=T.matrix('y')
>>> z=x**2+y**3
>>> f=theano.function([x,y],z)
>>> theano.printing.pydotprint(z, outfile="symbolic_graph_unopt.png",
                                  var_with_name_simple=True)
The output file is available at symbolic_graph_unopt.png
```


```bash
>>> theano.printing.pydotprint(f, outfile="symbolic_graph_opt.png",
                                  var_with_name_simple=True)
The output file is available at symbolic_graph_opt.png
```
