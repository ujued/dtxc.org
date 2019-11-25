---
layout: post
title:  "Linux 'ip route' 基础用法"
date:   2019-11-25 21:45:41 +0800
categories: linux net
---
```shell
# 添加一条路由，所有本机产生的或发往本机需要转发的到`CIDR`网络的IP数据包都将交由网卡`interface`发往`gateway`
ip route add `CIDR` via `gateway` dev `interface`


```