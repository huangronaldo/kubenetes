### 2 ubuntu部署k8s
> 以下为ubuntu 18.04部署k8s（单个master与单个node）实战记录片段。记录下来，以便后续查错与分享。步骤如下：

  * 机器环境信息
  * 初始化ubuntu环境配置
  * 安装docker
  * 安装kubeadm、kubectl以及kubelet
  * 初始化master节点
  * 将slave节点加入网络

#### 2.1 机器环境
> 准备两台服务器，信息如下：

| Node | OS | IP | CPU | Mem |
| --- | --- | --- | --- | --- |
| master01 | ubuntu 18.04 | 172.30.77.215 | 2C | 4G |
| worker01 | ubuntu 18.04 | 172.30.77.216 | 2C | 4G |

#### 2.2 修改ubuntu配置
> k8s 要求 管理节点可以直接免密登录工作节点 的原因是：在集群搭建完成后，管理节点的 kubelet 需要登陆工作节点进行操作。

* 关闭selinux
```
vim /etc/sysconfig/selinux
# 将SELINUX=disabled
setenforce 0
```

* SSH免密登录
```
# 生成证书，可指定生成文件和使用密码
ssh-keygen  -t rsa
ssh-copy-id -p22 -i ~/.ssh/id_rsa.pub root@172.30.77.216
```

> 如果是本地新建服务器，可以先修改软件源为国内的源，如阿里云源 / 清华源 / 中科院源等。
* 关闭swap，k8s要求系统需关闭swap，以便性能达到最大
```
# 第一步：直接关闭swap
swapoff -a
# 修改/etc/fstab注释配置信息，重启生效
vim /etc/fstab
```

* 时间同步
```
apt install ntpdate
ntpdate 0.rhel.pool.ntp.org
```

* 配置host
```
vim /etc/hosts
# 配置所有节点信息
172.30.77.215    master01
172.30.77.216    worker01

```

#### 2.3 安装docker

* 使用阿里云源进行安装
  * 允许apt通过https使用repository安装软件包
  ```
  apt-get update
  
  apt-get install \
     apt-transport-https \
     ca-certificates \
     curl \
     gnupg-agent \
     software-properties-common
  ```
  * 添加Docker 阿里GPG key
  ```
  curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | apt-key add -
  ```
  * 验证key的指纹
  ```
  apt-key fingerprint 0EBFCD88
  ```
  * 添加稳定版repository
  ```
  add-apt-repository \
   "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
  ```
  * 更新源
  ```
  apt-get update
  ```
  
* 安装docker
```
apt install docker.io
```
* 修改docker配置
```
vim /etc/docker/daemon.json
```
> 默认无该文件，创建后写入以下信息
```
{
  "registry-mirrors": [
    "https://registry.docker-cn.com",
    "http://hub-mirror.c.163.com",
    "https://docker.mirrors.ustc.edu.cn"
  ],
  "exec-opts": ["native.cgroupdriver=systemd"]
}
```
* 重启docker
```
systemctl daemon-reload
systemctl restart docker
```

#### 2.4 安装k8s
> 安装k8s主要是安装三个组件：kubelet、kubeadm以及kubectl，三个组件介绍如下：

* kubelet: k8s 的核心服务
* kubeadm: 这个是用于快速安装 k8s 的一个集成工具，我们在master01和worker01上的 k8s 部署都将使用它来完成。
* kubectl: k8s 的命令行工具，部署完成之后后续的操作都要用它来执行

```
# 使得 apt 支持 ssl 传输
apt-get update && apt-get install -y apt-transport-https
# 下载 gpg 密钥
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add - 
# 添加 k8s 镜像源
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF 
# 更新源列表
apt-get update
# 下载 kubectl，kubeadm以及 kubelet
apt-get install -y kubelet kubeadm kubectl
```

#### 2.5 安装Master节点
> 注意：以下所有命令只在master节点执行。

> 注意：以下所有命令只在master节点执行。

> 注意：以下所有命令只在master节点执行。

* 初始化master节点
* 部署flannel网络
* 配置kubectl工具
##### 初始化master节点
```
kubeadm init \
--apiserver-advertise-address=172.30.77.215 \
--image-repository registry.aliyuncs.com/google_containers \
--pod-network-cidr=10.244.0.0/16
```
使用kubeadm的init命令初始化master节点，一些常用参数描述如下：
* --apiserver-advertise-address: k8s中的主要服务apiserver的部署地址，填自己的管理节点IP
* --image-repository: 拉取的docker镜像源，因为初始化的时候kubeadm会去拉k8s的很多组件来进行部署，所以需要指定国内镜像源，下不然会拉取不到镜像。
* --pod-network-cidr: 这个是 k8s 采用的节点网络，因为我们将要使用flannel作为 k8s 的网络，所以这里填10.244.0.0/16就好
* --kubernetes-version: 这个是用来指定你要部署的 k8s 版本的，一般不用填，不过如果初始化过程中出现了因为版本不对导致的安装错误的话，可以用这个参数手动指定。
* --ignore-preflight-errors: 忽略初始化时遇到的错误，比如说我想忽略 cpu 数量不够 2 核引起的错误，就可以用--ignore-preflight-errors=CpuNum。错误名称在初始化错误时会给出来。

> 执行结果如下说明成功，请把最后那行以kubeadm join开头的命令复制下来，之后安装工作节点时要用到的，如果你不慎遗失了该命令，可以在master节点上使用kubeadm token create --print-join-command命令来重新生成一条。

```

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 172.30.77.215:6443 --token uq0wfs.uh35e515lz6c4vol \
    --discovery-token-ca-cert-hash sha256:3b1a30b9827fafbbd56d9944794a00196c6abb78f6c5e6cd49f098d75c5bea09 

```

##### 配置 kubectl 工具
```
mkdir -p /root/.kube && \
cp /etc/kubernetes/admin.conf /root/.kube/config
```
* kubectl测试命令
```
# 查看已加入的节点
kubectl get nodes
# 查看集群状态
kubectl get cs
```

##### 部署 flannel 网络
> flannel是一个专门为 k8s 设置的网络规划服务，可以让集群中的不同节点主机创建的 docker 容器都具有全集群唯一的虚拟IP地址。
> 注意最新DaemonSet版本已经成为apps/v1，采用以下方式。
```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
# 验证flannel网络插件是否部署成功（Running即为成功）
kubectl get pods -n kube-system |grep flannel   
```


#### 2.6  将 slave 节点加入网络
> 注意：以下所有命令只在worker节点执行。

> 注意：以下所有命令只在worker节点执行。

> 注意：以下所有命令只在worker节点执行。

> 紧接着2.4章节，完成前面的配置之后，将2.5节点中《初始化master节点》完成的命令执行，或者在master节点上使用kubeadm token create --print-join-command命令来重新生成一条。
```
kubeadm join 172.30.77.215:6443 --token uq0wfs.uh35e515lz6c4vol \
    --discovery-token-ca-cert-hash sha256:3b1a30b9827fafbbd56d9944794a00196c6abb78f6c5e6cd49f098d75c5bea09 
```
> 控制台结果如下即加入完成
```

This node has joined the cluster:
* Certificate signing request was sent to apiserver and a response was received.
* The Kubelet was informed of the new secure connection details.

Run 'kubectl get nodes' on the control-plane to see this node join the cluster.

```
查看查看节点情况
```
kubectl get nodes
```

#### 默认网卡问题修复
> 待完善

#### 完成
至此你应该已经搭建好了一个完整可用的双节点 k8s 集群了。
