---
title: tensorflow-gpu环境搭建
comments: true
categories:
  - ml
  - tensorflow
tags:
  - config
abbrlink: 1b96df2b
date: 2019-04-04 11:19:12
---

# tensorflow-gpu环境搭建

## 环境依赖

```text
Ubuntu-16.04.3-desktop-amd64
cuda_8.0.61_375.26_linux.run
cudnn-8.0-linux-x64-v7.tgz
Anaconda3-4.2.0-Linux-x86_64.sh
nvidia driver 384.111
```

## 换apt-get源

```text
deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse  
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse  
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse  
deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse  

deb http://mirrors.aliyun.com/ubuntu/ xenial-proposed main restricted universe multiverse  

deb-src http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse  
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse  
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse  
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse  

deb-src http://mirrors.aliyun.com/ubuntu/ xenial-proposed main restricted universe multiverse  

deb http://archive.canonical.com/ubuntu/ xenial partner  
deb http://extras.ubuntu.com/ubuntu/ xenial main  
```

## 安装vim

```bash
sudo apt-get remove vim-common
sudo apt-get install vim
```

## 安装gdebi

```bash
sudo apt-get install gdebi
```

## 禁用nouveau

```bash
sudo vim /etc/modprobe.d/blacklist.conf
```

在文件最后插入以下两行内容

```text
blacklist nouveau
options nouveau modeset=0
```

```bash
sudo update-initramfs -u
sudo reboot
```

```bash
lsmod | grep nouveau
没有输出则为成功
```

## gcc降版本

ubuntu的gcc编译器是5.4.0，然而cuda8.0不支持5.0以上的编译器，因此需要降级，把编译器版本降到4.9

```bash
sudo apt-get install g++-4.9
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.9 20
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-5 10
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-4.9 20
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-5 10
sudo update-alternatives --install /usr/bin/cc cc /usr/bin/gcc 30
sudo update-alternatives --set cc /usr/bin/gcc
sudo update-alternatives --install /usr/bin/c++ c++ /usr/bin/g++ 30
sudo update-alternatives --set c++ /usr/bin/g++
```

## 安装缺失的包

```bash
sudo apt-get install libglu1-mesa-dev libx11-dev libxi-dev libxmu-dev libgl1-mesa-dev
sudo updatedb
```

## 安装 cuda8.0

```bash
sudo service lightdm stop
alt+ctrl+F1
./cuda_8.0.61_375.26_linux.run
```

不要安装驱动和 opencl 和 X-config 其他默认,完成后

```bash
sudo service lightdm start
```

在确保 apt-get 源已经更新后，设置里 GUI 安装 nvidia 驱动384.111。

添加环境变量

```bash
sudo vi ~/.bashrc
```

最后加入以下几行

```bash
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda-8.0/lib64:/usr/local/cuda-8.0/extras/CUPTI/lib64"
export CUDA_HOME=/usr/local/cuda-8.0
```

## 安装 nvidia 工具包

```bash
sudo apt install nvidia-cuda-toolkit
sudo reboot
nvcc -V   //输出 nvidia 驱动的版本信息
```

## 测试 cuda 是否安装成功

```bash
cd /usr/local/cuda/samples/1_Utilities/deviceQuery
make .
./deviceQuery 输出 GPU 信息
```

## 安装 cuDNN

```bash
tar xvzf cudnn-8.0-linux-x64-v7.tgz

sudo cp cuda/include/cudnn.h /usr/local/cuda-8.0/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda-8.0/lib64
sudo chmod a+r /usr/local/cuda-8.0/include/cudnn.h /usr/local/cuda-8.0/lib64/libcudnn*
```

## 安装 Anaconda

```bash
./Anaconda-4.2.0 不要用 sudo
```

## conda 更新源

```bash
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --set show_channel_urls yes
vi ~/.condarc 把 default 去掉
```

## 安装 tensorflow

不用更新 conda 包列表,直接

```bash
conda install tensorflow-gpu
sudo reboot
```

## 测试 tensorflow

```python
import tensorflow as tf
a = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[2, 3], name='a')
b = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[3, 2], name='b')
c = tf.matmul(a, b)
sess = tf.Session(config=tf.ConfigProto(log_device_placement=True))
print(sess.run(c))
```

## References

`http://blog.csdn.net/zhaoyu106/article/details/52793183`

`https://segmentfault.com/a/1190000008234390`
