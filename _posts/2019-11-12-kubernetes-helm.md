---
layout: post
title:  "Kubernets Helm 介绍"
date:   2019-11-12 20:07:41 +0800
categories: nativecloud
---
Kubernets Helm发布包，有两个二进制文件，`helm`和`tiller`。tiller是以服务进程方式运行，依赖`～/.kube/config`和Kubernetes ApiServer交互，helm作为CLI和tiller server通讯。 helm把工程结果交给tiller, tiller做着`kubectl`的事情。
