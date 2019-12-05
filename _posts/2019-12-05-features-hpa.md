---
layout: post
title:  "ArgoRollouts之Pod水平自动伸缩（HPA）"
date:   2019-12-05 15:11:41 +0800
categories: nativecloud argo rollouts
---
> 当你决定使用Rollout这个CRD时，代表着你可以用它代替`Deployment`来发布你的应用

`HPA`是根据Kubernetes基于Cpu利用率或者用户设置的一些指标（metrics）来自动伸缩`Pods的数量`。为了完成这些行为，HPA仅通过两个字段来支持资源端点伸缩。被伸缩的端点资源允许HPA知道资源当前的状态，并合适的调整它。Argo Rollouts从在`0.3.0`发布版本开始支持端点伸缩。`Replicas`当被HPA修改以后，Argo Rollouts 控制器有责任使改变保持一致。使用Rollout后，策略将非常不同。Argo Rollouts控制器有不同的策略控制端点伸缩。下面是一些不同的策略：  

### 蓝绿（Blue Green）  
HPA会利用ReplicaSet从活跃的Servcie那里接受的流量指标并用`BlueGreen`策略伸缩 rollouts。当HPA更改副本数量时，Argo Rollouts控制器首先会扩大ReplicaSet  
TBD...

### 金丝雀（Canary）
The HPA will scale rollouts using the Canary Strategy using the metrics of all the ReplicasSets within the rollout. Since the Argo Rollouts controller does not control the service that sends traffic to those ReplicaSets, it assumes that all the ReplicaSets in the rollout are receiving traffic.


### 示例
下面是一个HPA利用CPU指标自动伸缩一个rollout的例子：  
```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: hpa-rollout-example
spec:
  maxReplicas: 6
  minReplicas: 2
  scaleTargetRef:
    apiVersion: argoproj.io
    kind: Rollout
    name: example-rollout
  targetCPUUtilizationPercentage: 80
```

### 要求
In order for the HPA to manipulate the rollout, the Kubernetes cluster hosting the rollout CRD needs the subresources support for CRDs. This feature was introduced as alpha in Kubernetes version 1.10 and transitioned to beta in Kubernetes version 1.11. If a user wants to use HPA on v1.10, the Kubernetes Cluster operator will need to add a custom feature flag to the API server. After 1.10, the flag is turned on by default. Check out the following link for more information on setting the custom feature flag.