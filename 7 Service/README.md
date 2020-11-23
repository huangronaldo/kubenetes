## 深入掌握Service
### Service 定义详解
Service定义文件完整内容如下：
```
apiVersion : v1                
kind: Service                  
metadata:                      
  name : string 
  namespace : string 
  labels: 
    - name: string 
  annotations: 
    - name: string 
spec: 
  selector: []
  type:string
  clusterIP: string
  sessionAffinity: string 
  ports: 
    - name: string 
      protocol: string 
      port: int 
      targetPort: int 
      nodePort: int 
  status: 
    loadBalancer: 
      ingress: 
        ip: string 
        hostname: string
 
```
定义详解：
![Image text](https://raw.githubusercontent.com/huangronaldo/kubernetes/master/res/service%E8%AF%A6%E8%A7%A31.png)
![Image text](https://raw.githubusercontent.com/huangronaldo/kubernetes/master/res/service%E8%AF%A6%E8%A7%A32.png)

### Service 基本用法
  一般来说，对外提供服务的应用程序需要通过某种机制来实现，对于容器应用最简便的方式就是通过 TCP/IP 机制及监听 和端口号来实现。直接通过 Pod 地址和端口号可以访问到容器应用内的服务，但是 Pod IP 地址是不可靠的，例如当 Pod 所在的 Node 发生故障时，Pod 将被 kubernetes 重新调度到另一台 Node,Pod IP 地址将发生变化。更重要的是，如果容器应用本身是分布式的部署方式，通过多个实例共同提供服务，就需要在这些实例的前端设置一个负载均衡器来实现请求的分发。 kubernetes中的service 就是设计出来用于解决这些问题的核心组件。

  目前 Kubemetes 提供了两种负载分发策略： RoundRobin和SessionAffinity ，具体说明如下。
  * RoundRobin ：轮询模式，即轮询将请求转发到后端的各个 Pod 上。
  * SessionAffinity ：基于客户端IP址进行会话保持的模式，即第1次将某个客户端发起的请求转发到后端的某个 Pod 上，之后从相同的客户端发起的请求都将被转发到后端相同的 Pod 上。

> 通过 Service 的定义， kubemetes 实现了一种分布式应用统一入口的定义和负载均衡机制。Service 还可以进行其他类型的设置，例如设置多个端口号、直接设置为集群外部服务，或实现为无头服务（ Headless ）模式。
* 多端口Service：一个容器应用可以提供多个端口的服务，可以设置多个不同端口号和同一端口号不同协议
 
* 外部服务Service：创建一个和Service同名的endpoint，用于指向实际的后端访问地址

* Headless Service：即不为 Service 设置 ClusterIP（入口地址），仅通Label Selector将后端的 pod 列表返回给调用的客户端


### 集群外部访问Pod或Service
由于Pod和Service是Kubernetes 集群访问内的虚拟概念，所以集群外的客户端系统无法通过Pod的IP地址或者Service的虚拟IP和端口访问到它们。为了让外部客户端可以访问这些服务，可以将Pod或Service 的端口映射到宿主机，以便客户端应用能通过物理机访问容器的应用。
* 将容器应用的端口号映射到物理机
  * 通过设置容器级别的hostPort，将容器应用的端口号映射到物理机上
  * 通过设置Pod级别的hostNetwork=true，该Pod所有容器的端口号将被直接映射到物理机上
* 将Service 的端口映射到物理机
  * 通过设置NodePort映射到物理机，同时设置Service 的类型为NodePort
  * 通过设置LoadBalancer，映射到服务商提供的LoadBalancer地址。




