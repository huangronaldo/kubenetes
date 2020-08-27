
### Kubenetes Names
> Kubernetes REST API中的所有对象都用Name和UID来明确地标识。

> 对于非唯一用户提供的属性，Kubernetes提供labels和annotations。

### Kubenetes Namespaces
> Kubernetes可以使用Namespaces（命名空间）创建多个虚拟集群。

#### 使用Namespaces
> Namespace的创建、删除和查看。
##### 创建
```
(1) 命令行直接创建
$ kubectl create namespace new-namespace

(2) 通过文件创建
$ cat my-namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: new-namespace

$ kubectl create -f ./my-namespace.yaml
```
##### 删除
```
$ kubectl delete namespaces new-namespace
```
##### 查看
```
$ kubectl get namespaces
```

