---
layout: post
title:  "Argo CD 核心概念（译）"
date:   2019-12-02 16:32:41 +0800
categories: nativecloud kubernetes argo cd
---

> 相必你已经非常熟悉 Git,Docker,Kubernetes,Continuous Delivery和GitOps等的核心概念了。  

* Application: 一组由配置文件定义的Kubernetes资源。是一个Kubernetes的`CRD`.  
* Application source type: 一个用来构建Application的工具（Tool）.  
* Target state: 一个应用（Application）渴望的状态，通常作为文件托管在Git仓库.  
* Live state: 一个应用的存活状态。像已部署的`Pods`.  
* Sync status: 同步状态，表示live state过渡到target state的进度。Git说的应用该是这个状态，已部署的应用做到了吗？（^-^）  
* Sync: 一次同步。使应用过渡到target state的一个进程。如应用一些改变到Kubernetes集群时会产生一个 `Sync`  
* Sync operation status: 同步结果。live state到target state过渡成功与否。  
* Refresh: 比较Git最新的代码和live state，指出不同之处。  
* Health: 应用的健康状况。表示者应用是否正确的运行，是否还可以处理请求。  
* Tool: 一个创建Kubernetes资源描述文件的工具，像Kustomize等。说白了，就是Application Source Type.  
* Configuration management tool： 说白了，就是 Tool  
* Configuration management plugin: 自定义的Tool

原文链接： [https://argoproj.github.io/argo-cd/core_concepts/](https://argoproj.github.io/argo-cd/core_concepts/)
