---
title: Anaconda更换清华源
comments: true
categories:
  - ml
  - python
tags:
  - config
abbrlink: da576ffb
date: 2019-04-04 11:05:42
---

# Anaconda更换清华源

Anaconda 安装包[下载](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/)

TUNA 还提供了 Anaconda 仓库的镜像，运行以下命令:

```bash
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --set show_channel_urls yes
```

即可添加 Anaconda Python 仓库
运行 `conda install numpy` 测试一下
