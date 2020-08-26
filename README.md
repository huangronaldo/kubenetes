# kubenetes学习笔记与总结
## 什么是kubenetes
  Kubernetes是容器集群管理系统，是一个开源的平台，可以实现容器集群的自动化部署、自动扩缩容、维护等功能。
### Kubernetes 特点 
  * 可移植: 支持公有云，私有云，混合云，多重云（multi-cloud）
  * 可扩展: 模块化, 插件化, 可挂载, 可组合
  * 自动化: 自动部署，自动重启，自动复制，自动伸缩/扩展
### Kubernetes可以做什么？
  使用Kubernetes能确保程序在任何时间、任何地方运行，还能扩展更多有需求的工具/资源。
  * 创建一个KUBERNETES集群
  * 部署应用程序
  * 查看应用程序
  * 发布应用程序
  * 扩展应用程序
  * 更新应用程序
### why containers ？
### kubenetes设计
#### kubenetes架构
Kubernetes借鉴了Borg的设计理念，比如Pod、Service、Labels和单Pod单IP等
* Kubernetes主要由以下几个核心组件组成：
  * etcd保存了整个集群的状态；
  * apiserver提供了资源操作的唯一入口，并提供认证、授权、访问控制、API注册和发现等机制；
  * controller manager负责维护集群的状态，比如故障检测、自动扩展、滚动更新等；
  * scheduler负责资源的调度，按照预定的调度策略将Pod调度到相应的机器上；
  * kubelet负责维护容器的生命周期，同时也负责Volume（CVI）和网络（CNI）的管理；
  * Container runtime负责镜像管理以及Pod和容器的真正运行（CRI）；
  * kube-proxy负责为Service提供cluster内部的服务发现和负载均衡；
* 除了核心组件，还有一些推荐的Add-ons：
  * kube-dns负责为整个集群提供DNS服务
  * Ingress Controller为服务提供外网入口
  * Heapster提供资源监控
  * Dashboard提供GUI
  * Federation提供跨可用区的集群
  * Fluentd-elasticsearch提供集群日志采集、存储与查询
#### kubenetes设计理念
* API设计原则
  * 所有API应该是声明式的
  * API对象是彼此互补而且可组合的
  * 高层API以操作意图为基础设计
  * 低层API根据高层API的控制需要设计
  * 尽量避免简单封装，不要有在外部API无法显式知道的内部隐藏的机制
  * API操作复杂度与对象数量成正比
  * API对象状态不能依赖于网络连接状态
  * 尽量避免让操作机制依赖于全局状态，因为在分布式系统中要保证全局状态的同步是非常困难的
* 控制机制设计原则
  * 控制逻辑应该只依赖于当前状态
  * 假设任何错误的可能，并做容错处理
  * 尽量避免复杂状态机，控制逻辑不要依赖无法监控的内部状态
  * 假设任何操作都可能被任何操作对象拒绝，甚至被错误解析
  * 每个模块都可以在出错后自动恢复
  * 每个模块都可以在必要时优雅地降级服务
#### kubernetes的核心技术概念和API对象
API对象是K8s集群中的管理操作单元。K8s集群系统每支持一项新功能，引入一项新技术，一定会新引入对应的API对象，支持对该功能的管理操作。例如副本集Replica Set对应的API对象是RS。

```
K8s系统最核心的两个设计理念：一个是容错性，一个是易扩展性。容错性实际是保证K8s系统稳定性和安全性的基础，易扩展性是保证K8s对变更友好，可以快速迭代增加新功能的基础。
```
## 基础概念
### 组件
#### Master组件
Master组件提供集群的管理控制中心。
##### kube-apiserver
```
kube-apiserver用于暴露Kubernetes API。任何的资源请求/调用操作都是通过kube-apiserver提供的接口进行。
```
##### ETCD
```
etcd是Kubernetes提供默认的存储系统，保存所有集群数据，使用时需要为etcd数据提供备份计划。
```
##### kube-controller-manager
``` 

kube-controller-manager运行管理控制器，它们是集群中处理常规任务的后台线程。逻辑上，每个控制器是一个单独的进程，但为了降低复杂性，它们都被编译成单个二进制文件，并在单个进程中运行。
```
  * 节点（Node）控制器。
  * 副本（Replication）控制器：负责维护系统中每个副本中的pod。
  * 端点（Endpoints）控制器：填充Endpoints对象（即连接Services＆Pods）。
  * Service Account和Token控制器：为新的Namespace 创建默认帐户访问API Token。
##### cloud-controller-manager
```
云控制器管理器负责与底层云提供商的平台交互。云控制器管理器是Kubernetes版本1.6中引入的，目前还是Alpha的功能。

云控制器管理器仅运行云提供商特定的（controller loops）控制器循环。可以通过将--cloud-provider flag设置为external启动kube-controller-manager ，来禁用控制器循环。
```
* cloud-controller-manager 具体功能：
  * 节点（Node）控制器
  * 路由（Route）控制器
  * 服务（Service）控制器
  * 卷（Volume）控制器
##### kube-scheduler
```
kube-scheduler 监视新创建没有分配到Node的Pod，为Pod选择一个Node。
```
##### 插件addons
``` 

插件（addon）是实现集群pod和Services功能的 。Pod由Deployments，ReplicationController等进行管理。Namespace 插件对象是在kube-system Namespace中创建。
```
* DNS: 群集DNS是一个DNS服务器，能够为Kubernetes services提供DNS记录。
* 用户界面: kube-ui提供集群状态基础信息查看。
* 容器资源监测: 容器资源监控提供一个UI浏览监控数据。
* Cluster-level Logging: 负责保存容器日志，搜索/查看日志。

#### Node组件


