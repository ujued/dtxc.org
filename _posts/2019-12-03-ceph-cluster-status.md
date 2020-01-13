---
layout: post
title:  "Ceph 集群状态常见问题"
date:   2019-12-03 10:27:41 +0800
categories: nativecloud storage ceph
---

1. 
`PG` 处于 stuck状态，一般是`osd`节点数太少，或是节点间权重分配有误。

```
# 调整OSD节点存储PG权重
ceph osd crush reweight osd.1 1.2
```
2. 
```
[root@ceph-mon0 ~]# ceph -s 
  cluster:
    id:     8345c764-cc94-402f-83f7-d4db29d79f89
    health: HEALTH_WARN
            1 slow ops, oldest one blocked for 257 sec, mon.ceph-mon0 has slow ops
```
这样一般是时钟不同步导致
