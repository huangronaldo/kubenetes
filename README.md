# kubernetes学习笔记与总结
## 什么是kubernetes
> Kubernetes是容器集群管理系统，是一个开源的平台，可以实现容器集群的自动化部署、自动扩缩容、维护等功能。
### Kubernetes 特点 
  * 可移植: 支持公有云，私有云，混合云，多重云（multi-cloud）
  * 可扩展: 模块化, 插件化, 可挂载, 可组合
  * 自动化: 自动部署，自动重启，自动复制，自动伸缩/扩展
### Kubernetes可以做什么？
> 使用Kubernetes能确保程序在任何时间、任何地方运行，还能扩展更多有需求的工具/资源。
  * 创建一个KUBERNETES集群
  * 部署应用程序
  * 查看应用程序
  * 发布应用程序
  * 扩展应用程序
  * 更新应用程序
### why containers ？
### kubernetes设计
#### kubernetes架构
> Kubernetes借鉴了Borg的设计理念，比如Pod、Service、Labels和单Pod单IP等
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
#### kubernetes设计理念
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
#### kubernetes分层架构

* 核心层：Kubernetes最核心的功能，对外提供API构建高层的应用，对内提供插件式应用执行环境
* 应用层：部署（无状态应用、有状态应用、批处理任务、集群应用等）和路由（服务发现、DNS解析等）
* 管理层：系统度量（如基础设施、容器和网络的度量），自动化（如自动扩展、动态Provision等）以及策略管理（RBAC、Quota、PSP、NetworkPolicy等）
* 接口层：kubectl命令行工具、客户端SDK以及集群联邦
* 生态系统：在接口层之上的庞大容器集群管理调度的生态系统，可以划分为两个范畴
  * Kubernetes外部：日志、监控、配置管理、CI、CD、Workflow、FaaS、OTS应用、ChatOps等
  * Kubernetes内部：CRI、CNI、CVI、镜像仓库、Cloud Provider、集群自身的配置和管理等
#### kubernetes的核心技术概念和API对象
> API对象是K8s集群中的管理操作单元。K8s集群系统每支持一项新功能，引入一项新技术，一定会新引入对应的API对象，支持对该功能的管理操作。例如副本集Replica Set对应的API对象是RS。

> K8s系统最核心的两个设计理念：一个是容错性，一个是易扩展性。容错性实际是保证K8s系统稳定性和安全性的基础，易扩展性是保证K8s对变更友好，可以快速迭代增加新功能的基础。

## 基础概念
### Kubernetes组件
### Kubernetes对象
### Kubernetes Names
### Kubernetes Namespaces


