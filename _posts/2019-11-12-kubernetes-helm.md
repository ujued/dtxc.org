---
layout: post
title:  "Kubernets Helm 介绍"
date:   2019-11-12 20:07:41 +0800
categories: nativecloud
---
Kubernets Helm由 `helm cli`和`tiller server`组成，`helm cli`把helm工程结果交给`tiller`, `tiller`和k8s`apiController`通讯，做着`kubectl`的事情。
