# 3 部署Dashboard
## Dashboard简介
> Dashboard 是基于网页的 Kubernetes 用户界面。您可以使用 Dashboard 将容器应用部署到 Kubernetes 集群中，也可以对容器应用排错，还能管理集群本身及其附属资源。您可以使用 Dashboard 获取运行在集群中的应用的概览信息，也可以创建或者修改 Kubernetes 资源（如 Deployment，Job，DaemonSet 等等）。例如，您可以对 Deployment 实现弹性伸缩、发起滚动升级、重启 Pod 或者使用向导创建新的应用。

> k8s Dashboard的github地址：https://github.com/kubernetes/dashboard，需下载yaml文件。

> 如果githubusercontent.com无法访问，添加 `199.232.4.133 raw.githubusercontent.com` 至/etc/hosts文件中。

## 基于k8s部署apply直接部署Dashboard

* 先获取github 上 yaml文件
```
wget -O "kubernetes-dashboard.yaml" https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.4/aio/deploy/recommended.yaml
```
* 修改yaml文件
> service模块配置暴露k8s集群外部访问端口，配置如下：
```
kind: Service
apiVersion: v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kube-system
spec:
  type: NodePort
  ports:
    - port: 443
      targetPort: 8443
      nodePort: 30001
  selector:
    k8s-app: kubernetes-dashboard
```

## 启动Dashboard
```
kubectl create -f kubernetes-dashboard.yaml 
```

## 访问Dashboard
> https://master-ip:30001，注意：需要使用`https`

> 登陆会有两种方式：kubeconfig和token

## 使用令牌登录（需要创建能够访问 Dashboard 的用户）
> 创建account.yaml文件
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kube-system
---
# Create ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kube-system
```
* 执行account.yaml创建用户
```
kubectl create -f account.yaml
```
* 获取token
```
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
```
## dashboard访问方式
### kubectl proxy 访问
```
nohup kubectl proxy --address=172.30.77.215 --disable-filter=true &
```

访问地址：
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login
