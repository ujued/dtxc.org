---
layout: post
title:  "Kubernetes StatefulSets"
date:   2019-11-13 09:29:41 +0800
categories: nativecloud kubernetes
---
> 和`Deployment`一样，有相同的`spec`字段描述来管理`Pods`。和Deployment不同的是，StatefullSets为所管理的 Pods 及其严格的维持一个标识。那些Pods虽然用一样的spec描述而创建，但是每一个都是不可替换的： 在任何调度中。每一个Pod都维持这个标识符。（来自官方文档的翻译）

> StatefulSets特征：
  稳定的，唯一的网络标识  
  稳定的，持久化存储  
  有序的，优雅部署和扩容
  有序的，自动回滚更新  
  上面所说的稳定是指，在任何调度中，Pods都是稳定的，不会因为调度而发生改变。如果不需要上面任何特性，请用  Deployment 或 ReplicaSet 来部署你没有状态的应用。

 


