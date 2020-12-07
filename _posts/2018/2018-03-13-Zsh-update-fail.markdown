---
layout: "post"
title: "zsh update fail"
date: "2018-03-13 16:39"
comments: true
---


#### 1. Get the config file
```bash
./buildconf
```

#### 2. Config with openssl
you need to install `openssl` first, then run the code below.
```bash
./configure --with-ssl
make
make test #(optional)
make install
```
