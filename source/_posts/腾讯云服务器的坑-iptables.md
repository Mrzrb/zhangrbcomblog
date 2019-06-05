---
title: 腾讯云服务器的坑-iptables
date: 2019-04-08 14:13:39
tags: iptables
keywords:
description:
---

# 腾讯云服务器的坑-iptables

## iptables 没法加载 nat 模块

今天安装 docker 的时候发现启动失败， journalctl -u docker.service 发现无法通过 iptables 设置 nat

google 之后发现这位大佬也遇到了这个问题， 有可能是因为 linux 禁用了一些系统模块

[Docker 安装指南以及使用 GPU](https://bluesmilery.github.io/blogs/252e6902/)

详细细节可以看大佬的文章

```bash
/etc/modprobe.d/blacklist.conf

/etc/modprobe.d/connectiontracking.conf

```

这两个文件就是模块黑名单，去掉对应的 mod 即可
