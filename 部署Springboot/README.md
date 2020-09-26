#### 构建docker镜像
* 编写Dockerfile

```
# Docker image for springboot
# VERSION 1.0.0
# Author: huangt
# 基础镜像使用java
FROM openjdk:8-jdk-alpine
# 作者
MAINTAINER huangt <huangronaldo@qq.com>

ENV TZ=Asia/Shanghai
RUN ln -snf /usr/sfhare/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
#容器中创建目录
RUN mkdir -p /app/test
# VOLUME 指定了临时文件目录为/app/test。
# 其效果是在主机 /var/lib/docker 目录下创建了一个临时文件，并链接到容器的/app/test
VOLUME /app/test
# 将jar包添加到容器中并更名为app.jar
ADD dcoffice-management-1.0.2.jar /app/test/app.jar 
ADD application.yml /app/test/application.yml

# 指定容器启动时要执行的命令
# ENTRYPOINT ["sh", "/app/test/entrypoint.sh"]
ENTRYPOINT ["nohup", "java","-Djava.security.egd=file:/dev/./urandom -Duser.timezone=GMT+8", "-jar", "/app/test/app.jar", "&"]
```
* 构建镜像
```
docker build -t springboot-demo .
```

* 上传镜像
> 因国内拉取docker hub速度比较慢，所以先选国内的仓库，可以去阿里云注册账号

```
# 登陆
docker login --use#rname=huangronaldo@qq.com registry.cn-hangzhou.aliyuncs.com
# 打标签
docker tag 2aaf2f487b21 registry.cn-hangzhou.aliyuncs.com/huangt/springboot-demo:1.0.0-test
# 提交推送
docker push registry.cn-hangzhou.aliyuncs.com/huangt/springboot-demo:1.0.0-test
```

#### 编写k8s
* springboot-rc.yml

```
apiVersion: v1
kind: Service
metadata:
  name: springboot-demo
  namespace: default
  labels:
    app: springboot-demo
spec:
  type: NodePort
  ports:
  - port: 8881
    nodePort: 30090 #service对外开放端口
  selector:
    app: springboot-demo
---
apiVersion: apps/v1
kind: Deployment #对象类型
metadata:
  name: springboot-demo #名称
  labels:
    app: springboot-demo #标注 
spec:
  replicas: 3 #运行容器的副本数，修改这里可以快速修改分布式节点数量
  selector:
    matchLabels:
      app: springboot-demo
  template:
    metadata:
      labels:
        app: springboot-demo
    spec:
      containers: #docker容器的配置
      - name: springboot-demo
        image: registry.cn-hangzhou.aliyuncs.com/huangt/test:springboot-demo # pull镜像的地址 ip:prot/dir/images:tag
        imagePullPolicy: IfNotPresent #pull镜像时机，
        ports:
        - containerPort: 8881 #容器对外开放端口
```
* 创建容器
```
kubectl create -f springboot-rc.yml
```
* 删除容器
```
kubectl delete -f springboot.yml 
```
* 查看容器
```
kubectl get pods
```
* 查看容器日志
```
kubectl logs 容器id
```
