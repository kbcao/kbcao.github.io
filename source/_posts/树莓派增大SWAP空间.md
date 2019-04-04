---
title: 树莓派增大SWAP空间
comments: true
categories:
  - RaspberryPi
tags:
  - config
abbrlink: 9268dc38
date: 2019-04-04 11:35:41
---

# 树莓派增大SWAP空间

1. `cd /var`

2. 先关闭 `sudo swapoff /var/swap`

3. 重新设置大小1M * 1024 = 1G `sudo dd if=/dev/zero of=swap bs=1M count=1024`

4. 格式化 `sudo mkswap /var/swap`

5. 开启 `sudo swapon /var/swap`

6. 查看当前已生效的swap `swapon -s`

7. 查看当前swap使用情况 `free -m`