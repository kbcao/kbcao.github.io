---
title: Anaconda环境管理
comments: true
categories:
  - ml
  - python
tags:
  - null
abbrlink: 5ee72591
date: 2019-06-23 15:55:05
---

# Anaconda环境管理

创建环境
conda create -n py35 python=3.5

进入环境
Linux: source activate py35
Windows：activate py35

退出环境
Linux: source deactivate
Windows：deactivate

列出环境
conda env list

移除
conda remove -n py35 --all

搜索包
conda search mxnet*

指定环境，安装指定版本的包
conda install -n py35 mxnet==1.0.0

指定环境，更新包
conda update -n py35 mxnet

指定环境，查看已安装的包
conda list -n py35

指定环境，删除包
conda remove -n py35 mxnet
