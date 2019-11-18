---
layout: post
title:  "Docker Swarm 注意事项"
date:   2019-11-13 16:11:41 +0800
categories: nativecloud docker swarm
---

在网络方面，需要在路由器和防火墙中开放如下端口。  
`2377/tcp`：用于客户端与 Swarm 进行安全通信。  
`7946/tcp` 与 7946/udp：用于控制面 gossip 分发。  
`4789/udp`：用于基于 VXLAN 的覆盖网络。  
