---
layout: post
title:  "Kubernets kubectl 工具使用介绍（官方文档翻译）"
date:   2019-11-29 14:34:41 +0800
categories: nativecloud kubernetes
---

### kubectl概览
1. 列出Kubernetes资源  

```shell

# 查看`kube-system`命名空间下所有 `Deployment`资源
kubectl get deployments --namespace kube-system

# 查看`kube-system`命名空间下`kube-dns`这个`Deployment`资源的详细信息
kubectl describe deployment kube-dns --namespace kube-system

```

2. 从配置文件中创建资源  

```shell

# 从远程的一个配置文件创建或更新一个Kubernetes资源
kubectl apply -f https://raw.githubusercontent.com/kubernetes/kubectl/master/docs/book/examples/nginx/nginx.yaml

# 从本地的一个配置文件创建或更新一个Kubernetes资源
kubectl apply -f ./examples/nginx/nginx.yaml

# 根据配置文件查看应用的资源情况
kubectl get -f ./examples/nginx/nginx.yaml --show-labels
```

3. 从命令行生成一个配置文件  

```shell
# `--dry-run` 参数指明本次命令仅是为了输出要传送给APIServer的内容
# 需要注意的是，本次输出由`Go`对象序列化产生的，会存在一些值为`Zero`的字段，你可以删掉他们
kubectl create deployment nginx --dry-run -o yaml --image nginx
```

4. 查看通过Kubernetes资源创建的`Pods`  

```shell
# `-l`选项指定了标签筛选条件，创建资源时会指定一些`Labels`
kubectl get pods -l app=nginx
```

5. 调试容器  

```shell
# 输出Label是nginx的所有Pods的日志
kubectl logs -l app=nginx

# 获取一个特定Pod的`Shell`。需要注意的是，Pod运行多个容器时，还要指定容器名
kubectl exec -i -t  nginx-deployment-5c689d88bb-s7xcv [containerName] bash
```

### Kubernetes资源（Resources）和控制器（Controllers）概览

1. 资源（Resources）  
像Deployments, Services, Namespaces等Kubernetes对象的实例称为资源。  

运行容器的资源常常叫做工作负载（Workloads）。

Deployments，StatefulSets，Jobs，CronJobs，DaemonSets等都是工作负载。  

用户通过定义一些文件交给资源APIs（Resources APIs）来应用到Kubernetes集群。那些文件常常被称为资源配置（Resouce Config）  

一般通过 
* apiVersion (API Type Group and Version)
* kind (API Type Name)
* metadata.namespace (Instance namespace)
* metadata.name (Instance name)
来唯一标识资源（Resources）

2. 资源结构（Resources Structure）  
一般地，资源有如下组件构成：  
TypeMeta: Resource Type apiVersion and kind.  

ObjectMeta: Resource name and namespace + other metadata (labels, annotations, etc).  

Spec: the desired state of the Resource - intended state the user provides to the cluster.  

Status: the observed state of the object - recorded state the cluster provides to the user.  

Resource Config written by the user omits the Status field.  

一个简单示例Deployment资源的资源配置（Resource Config）如下：  
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.4
```
> 一些像ConfigMaps， Secrets等资源不存在状态信息，他们不需要描述，所以他们没有Spec字段。

3. 控制器（Controllers）  
控制器提供Kubernetes APIs。他们观察系统到的状态，监听到资源（像资源的创建更新和删除）或者系统（像Pod或主机节点死掉）的变化，然后尽职尽责的执行用户（如通过资源配置）或自动化（如自动伸缩）的指示。  

比如，当用户创建一个Deployment, 如果这个Deployment存在，Deployment控制器（Deployment Controller）会检查对比已存在的 ReplicaSet，如果不存在，则会创建ReplicaSet。

4. 控制器结构（Controller Structure）  
* 协调作用  
Controllers actuate Resources by reading the Resource they are Reconciling + related Resources, such as those that they create and delete.  
控制器不关心事件，只有当期望的状态和集群现有状态不一致时才开始协调。  
1.Deployment控制器创建/删除ReplicaSets  
2.ReplicaSet控制器创建/删除Pods  
3.Scheduler(控制器)关联主机节点到Pods  
4.Node(控制器)在节点运行Pods里的容器  
* 监视作用  
Controllers actuate Resources after they are written by Watching Resource Types, and then triggering Reconciles from Events. After a Resource is created/updated/deleted, Controllers Watching the Resource Type will receive a notification that the Resource has been changed, and they will read the state of the system to see what has changed (instead of relying on the Event for this information).  

1.Deployment控制器监视Deployments + ReplicaSets (+ Pods)  
2.ReplicaSet控制器监视ReplicaSets + Pods  
3.Scheduler(控制器)监视Pods  
4.Node(控制器)监视Pods(+ Secrests + ConnfigMaps )

### Kubernetes资源APIs(Kubernetes Resource APIs)
1. Pods  
容器（Containers）运行在调度到主机的节点上的Pods里面。  
Pods运行一个应用的单副本并提供如下：  
Compute Resources (cpu, memory, disk)  
Environment Variables  
Readiness and Health Checking  
Network (IP address shared by containers in the Pod)  
Mounting Shared Configuration and Secrets  
Mounting Storage Volumes  
Initialization  

2. 工作负载（Workloads）  
TBD...
