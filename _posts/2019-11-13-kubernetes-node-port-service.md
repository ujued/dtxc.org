---
layout: post
title:  "什么是Kubernetes NodePort Service"
date:   2019-11-13 09:26:41 +0800
categories: nativecloud
---
在安装Kubernetes Cluster时，每个Worker Node都放开了 `30000-32767`端口，这些端口就是为NodePort Service申请的，当该类Service创建成功后随即在此范围内申请一个端口号，在该Service被删除之前是不被改变的，也就是说该端口为这次部署服务的固定端口，对外提供服务。
