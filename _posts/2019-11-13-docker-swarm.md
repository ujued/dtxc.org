---
layout: post
title:  "Docker Swarm 注意事项"
date:   2019-11-13 16:11:41 +0800
categories: nativecloud docker swarm
---  

### Swarm架构
有很多装有Docker的主机节点组成，大致分为两类节点，`Manager`管理节点和`Worker`工作节点。Manager类似于Kubernets集群的`Control Plane`，管理工作节点，负责`Service`（Swarm Stack 有一组 Service组成，区别于Kubernets的Service，这个更像是Kubernets的Pod）调度，实现`RAFT`协议做为高可用管理节点间的状态同步

### 网络方面
需要在路由器和防火墙中开放如下端口。  
`2377/tcp`：用于客户端与 Swarm 进行安全通信。  
`7946/tcp` 与 7946/udp：用于管理节点间 gossip 分发。  
`4789/udp`：用于基于 VXLAN 的覆盖网络（overlay）。

