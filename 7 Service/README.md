## 深入掌握
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
一般来说，对外提供服务的应用程序需要通过某种机制来实现，对于容器应用最简便的方式就是通过 TCP/IP 机制及监听 和端口号来实现。直接通过 Pod 地址和端口号可以访问到容器应用内的服务，但是 Pod IP 地址是不可靠的，例如当 Pod 所在的 Node 发生故障时，Pod 将被 kubernetes 重新调度到另一台 Node,Pod IP 地址将发生变化。更重要的是，如果容器应用本身是分布式的部署方式，通过多个实例共同提供服务，就需要在这些实例的前端设置一个负载均衡器来实现请求的分发。 kubernetes中的service 就是设计出来用于解决这些问题的核组件。



