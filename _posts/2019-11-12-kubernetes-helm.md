---
layout: post
title:  "Kubernets Helm"
date:   2019-11-12 20:07:41 +0800
categories: nativecloud
---
### Helm2
Kubernets Helm发布包，有两个二进制文件，`helm`和`tiller`。

tiller是以服务进程方式运行，依赖`～/.kube/config`和Kubernetes ApiServer交互，helm作为CLI和tiller server通讯。

### Helm3
仅有一个二进制文件，直接和Kubernets API交互。允许在不同命名空间下发布相同名称的`RELEASE`。
