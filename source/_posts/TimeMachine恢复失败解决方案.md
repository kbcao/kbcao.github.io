---
title: TimeMachine恢复失败解决方案
comments: true
categories:
  - Lifestyle
tags:
  - config
abbrlink: 468c0eb9
date: 2019-04-04 11:22:24
---

# TimeMachine恢复失败解决方案

系统中某些软件可能会出现有空格的文件夹名字，导致恢复失败，详情可以通过Windows菜单查看日志  
需要删除备份中的文件夹，才能恢复成功  
删除需要用terminal（可以在recover的terminal下操作）

```bash
/System/Library/Extensions/TMSafetyNet.kext/Contents/Helpers/bypass rm -rf /Volumes/.../...此处为log中显示无法复制的带空格的文件夹
```
