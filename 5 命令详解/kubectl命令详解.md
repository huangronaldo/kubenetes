### kubectl 命令行用法与详解
#### kubectl 用法概述
kubectl 命令行的语法如下：
```
$ kubectl [command] [TYPE] [NAME] [flags]
```
其中，command / type / name / flags 的含义如下：
* command：子命令，用于操作kubernetes集群资源对象的命令，例如create / delete / describe / get / apple 等
* TYPE：资源对象的类型，区分大小写，能以单数形式 / 复式或简写形式表示，如pod / pods / po
* NAME：资源对象的名称，区别大小写，如果不指定名称，则系统返回属于TYPE的全部对象列表。
* flags：kubectl 子命令的可选参数，例如 “-s” 指定apiserver的url地址，而不用默认值

#### kubectl 操作示例
* 创建资源对象
根据yaml配置文件一次性创建serice和rc：
```
$ kubectl create -f service.yaml -f rc.yaml
```
根据<directory>目录下所有.yaml .yml .json 文件的定义进行创建操作：
```
$ kubectl create -f <directory>
```

* 查看资源对象
查看所有pod列表：
```
$ kubectl get pods
```
查看rc 和 service列表：
```
$ kubectl get rc,service
```

* 描述资源对象
显示Node的详细信息
```
$ kubectl describe nodes <node - name>
```
显示 Pod 的详细信息：
```
$ kubectl describe pods/<pod- name>
```
显示由 RC 管理的Pod的信息：
```
$ kubectl describe nodes <node - name>
```


* 删除资源对象
基于pod.yaml 定义的名称删除pod：
```
$ kubectl delete -f pod.yaml
```
删除所有包含某个label的pod和service：
```
$ kubectl delete pods, services -1 name=<label-name>
```
删除所有pod：
```
$ kubectl delete pods --all
```

* 执行容器的命令
执行pod的date命令，默认使用pod中的第一个容器执行：
```
$ kubectl exec <pod-name> date
```
指定pod中某个容器执行date命令：
```
$ kubectl exec <pod-name> -c <container-name> date
```
通过bash获得Pod中某个容器的TTY ，相当于登录容器：
```
$ kubectl exec -it <pod-name> -c <container-name> /bin/bash
```

* 查看容器的日志
查看容器输出到stdout的日志：
```
$ kubectl logs <pod-name> 
```
跟踪查看容器的日志，相当于 tail -f 命令的结果：
```
$ kubectl logs -f <pod-name> -c <container-name>
```





