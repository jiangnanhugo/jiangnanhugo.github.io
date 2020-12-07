---
layout: "post"
title: "Tensorflow python [defunct]"
date: "2016-06-04 11:12"
comments: true
---

在ubuntu上面安装了GPU版本的tensorflow后，很容易碰到 `zombie thread` 的问题，无法正常关闭tensorflow的线程，用 `ps aux|grep python` 可以看到:
```bash
python [defunct]
```
表明这个python 的程序已经成为了`zombie`了，如果要杀死该进程，必须要kill 其parent 的进程。

然而，不信的是我们发现 `PPID=1` ， 这个是系统默认的INIT进程，是不能kill的，所以除了手动重启没有什么好的解决方法。

### Solution
然而，这个问题主要是nvidia的驱动没写好，有人发现最新的nvidia 已经修复了该问题，所以我们只需要更新`nvidia driver`就好了。
1. 首先我们可以考虑删除原先安装的nvidia 驱动（此步非必需）

```bash
sudo apt-get uninstall --purge nvidia-*
```

2. 为了安装最新版的驱动，我们需要添加源：

```bash
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt-get update
sudo apt-get install nvidia-361
```

3. 更新完驱动要重启

```bash
sudo reboot
```

4. 查看nvidia设备是否已经安装好了最新的驱动。

```bash
nvidia-smi
# NVIDIA-SMI 361.45     Driver Version: 361.45.11
```

5. 然后tensorflow就可以正常使用了~
