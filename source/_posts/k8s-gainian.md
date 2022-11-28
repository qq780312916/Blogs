---
title: Kubernetes组件概念
abbrlink: 9b833745
date: 2022-11-28 15:53:15
tags:
  - kubernetes
categories: Kubernetes
cover: img/fengmian/k8s.jpeg
---
# Kubernetes组件

![Kubernetes 的组件](https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg)

## 控制平面组件

- kube-apiserver

  负责公开了 Kubernetes API，负责处理接受请求的工作

- etcd

  高度可用的键值存储，k8s集群的数据库

- kube-scheduler

   负责监视新调度创建的、未指定运行节点的Pods， 并选择节点来让 Pod 在上面运行

  调度决策考虑的因素包括单个 Pod 及 Pods 集合的资源需求、软硬件及策略约束、 亲和性及反亲和性规范、数据位置、工作负载间的干扰及最后时限

- kube-controller-manager

  - **节点控制器（Node Controller）**

    负责在节点出现故障时进行通知和响应

  - **任务控制器（Job Controller）**

    监测一次性任务的Job对象，然后创建Pods来运行这些任务直至完成

  - **端点分片控制器（EndpointSlice Controller）**

    填充端点分片(EndPointSlice)对象(对SVC和Pod之间进行关联)

  - **服务账号控制器（Service Account Controller）**

    为新的名称空间创建默认的服务账号（SA）

- cloud-conroller-manager

  允许将集群连接到云提供商的 API 之上， 并将与该云平台交互的组件与集群交互的组件分离开来。

  - **节点控制器（Node Controller）**

    在节点终止响应后检查云提供商以确定节点是否已删除

  - **路由控制器（Route Controller）**

    用于在底层云基础架构中设置路由

  - **服务控制器（Service Controller）**

    用于创建、更新和删除云提供商负载均衡器

## Node组件

- kubelet

  kubelet运行在集群中每个节点上。它保证容器都运行在Pod中

  kubelet接受到来自控制平面提供的PodSpecs，确保这些PodSpecs中描述的容器处于运行且状态健康。kubelet不会管理不是由k8s创建的容器

- kube-proxy

  kube-proxy运行集群中每个节点上的网络代理，实现k8s服务(Service)的一部分。

  维护节点上的网络规则，这些网络规则会允许从集群内部或外部的网络会话与Pod进行网络通信

- 容器运行时(Container Runtime)

  容器运行环境是负责运行容器的软件

  containerd、CRI-O、Docker(1.24开始需要安装cri-dockerd)、Podman等遵循CRI标准