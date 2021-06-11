本篇源码分析基于 k8s v1.21 版本

## 1. kube-apiserver 功能概述

​	Kubernetes API Server 是由 kube-apiserver 进程实现的，它运行在 Kubernetes 的管理节点上，并对外提供 Kubernetes Restful API 服务，主要提供与集群管理相关的 API 服务，如校验 pod、service、replication controller 的配置并存储到后端的 Etcd Server 上。



kube-apiserver 提供了三种 http server，将庞杂的 kube-apiserver 组件进行解耦：

> 三种服务底层都依赖 GenericAPIServer，通过 GenericAPIServer 可以将k8s资源与 rest api 进行映射

| 服务名              | 概述                                                         | 对象管理                  | 资源注册表                 |
| ------------------- | ------------------------------------------------------------ | ------------------------- | -------------------------- |
| KubeAPIServer       | 核心服务，提供k8s内置核心资源服务，不允许开发者随意修改，如：Pod，Service等 | Master                    | Legacyscheme.Scheme        |
| APIExtensionsServer | API扩展服务，该服务提供了 `CRD` 自定义资源服务               | CustomResourceDefinitions | extensionsapiserver.Scheme |
| AggregatorServer    | API聚合服务，提供了聚合服务                                  | APIAggregator             | aggregatorscheme.Scheme    |

## 2. kube-apiserver 启动流程概述

kube-apiserver 启动较为复杂，但是大体可以分为9个步骤：

​	（1）资源注册（Scheme）

​	（2）Cobra 命令行参数解析

​	（3）创建 apiserver 通用配置

​	（4）创建 APIExtensionsServer

​	（5）创建 KubeAPIServer

​	（6）创建 AggregatorServer

​	（7）创建 GenericAPIServer

​	（8）启动http服务

​	（9）启动https服务

其中 `APIExtensionsServer` `KubeAPIServer` 和 `AggregatorServer`  是通过委托模式链接在一起的，内部初始化过程类似，如下：

* 首先为每个 server 创建对应的 config 
* 然后初始化 http server，包括：
  * 初始化 GoRestfulContainer
  * 安装 server 所包含的 api，包括：
    * 为每个api-resource创建对应的后端存储RESTStorage
    * 为每个api-resource所支持的verbs添加对应的handler
    * 将handler注册到router中
    * 将router注册到webservice

## 3. kube-apiserver 源代码分析

### 0. 入口函数

### 1. 资源注册

### 2. 服务链创建



