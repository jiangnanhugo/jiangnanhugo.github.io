---
layout: "post"
title: "Cases for beautiful codes in python"
date: "2017-03-03 11:11"
comments: true
---

Notes from Raymond Hettinger's talk at pycon US 2013：
- [Transforming Code into Beautiful, Idiomatic Python](http://www.youtube.com/watch?feature=player_embedded&v=OSGv2VnC0go),
- [Transforming code into Beautiful, Idiomatic Python by Raymond Hettinger](https://speakerdeck.com/pyconslides/transforming-code-into-beautiful-idiomatic-python-by-raymond-hettinger-1),
- [Gist Doc](https://gist.github.com/JeffPaine/6213790).


#### 1. Looping over a range of numbers

```python
# these two for loop method are equal.
for i in [0,1,2,3,4,5]:
  print i
for i in range(6):
  print i
# Better method: use xrange in python 2.7; use range in python3
for i in xrange(6):
  print i
```

#### 2. Looping backward
```python
colors = ['red', 'green', 'blue', 'yellow']
for i in range(len(colors)-1, -1, -1):
    print colors[i]
# Better method: use reversed
for color in reversed(colors):
    print color
```

#### 3. looping over collection and indices

```python
colors = ['red', 'green', 'blue', 'yellow']
for i in range(len(colors)):
    print i, '--->', colors[i]
# Better Method: use enumerate
for i, color in enumerate(colors):
    print i, '--->', color
```

#### 4. Distinguishing multiple exit points in loops

```python
def find(seq, target):
    found = False
    for i, value in enumerate(seq):
        if value == target:
            found = True
            break
    if not found: return -1
    return i
##### Better: use "for else"
def find(seq, target):
    for i, value in enumerate(seq):
        if value == target: break
    else: return -1
    return i
```


#### 5. Looping over dictionary keys and values

```python
# Not very fast, has to re-hash every key and do a lookup
for k in d:
    print k, '--->', d[k]
# Makes a big huge list
for k, v in d.items():
    print k, '--->', v
# Better use: iteritems()
for k, v in d.iteritems():
    print k, '--->', v
```
`iteritems()` is better as it returns an iterator.


#### 6. Counting with dictionaries

```python
colors = ['red', 'green', 'red', 'blue', 'green', 'red']
# Simple, basic way to count. A good start for beginners.
d = {}
for color in colors:
    if color not in d: d[color] = 0
    d[color] += 1
# {'blue': 1, 'green': 2, 'red': 3}
# Slightly more modern but has several caveats, better for advanced users
# who understand the intricacies
d = defaultdict(int)
for color in colors:
  d[color] += 1
```

#### 7. Grouping with dictionaries

```python
names = ['raymond', 'rachel', 'matthew', 'roger',
         'betty', 'melissa', 'judith', 'charlie']
# In this example, we're grouping by name length
d = {}
for name in names:
    key = len(name)
    if key not in d:
        d[key] = []
    d[key].append(name)
# {5: ['roger', 'betty'], 6: ['rachel', 'judith'], 7: ['raymond', 'matthew', 'melissa', 'charlie']}
##### Better
d = defaultdict(list)
for name in names:
    key = len(name)
    d[key].append(name)
```
