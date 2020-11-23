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

