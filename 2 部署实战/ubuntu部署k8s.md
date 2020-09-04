### ubuntu部署k8s
> 以下为ubuntu 18.04部署k8s（单个master与单个node）实战记录片段。记录下来，以便后续查错与分享。步骤如下：

  * 机器环境信息
  * 修改ubuntu配置
  * 安装docker
  * 安装kubeadm、kubectl以及kubelet
  * 初始化master节点
  * 将slave节点加入网络

#### 机器环境
> 准备两台服务器，信息如下：

| Node | OS | IP | CPU | Mem |
| --- | --- | --- | --- | --- |
| master01 | ubuntu 18.04 | 172.30.77.215 | 2C | 4G |
| worker01 | ubuntu 18.04 | 172.30.77.216 | 2C | 4G |

#### 修改ubuntu配置
> k8s 要求 管理节点可以直接免密登录工作节点 的原因是：在集群搭建完成后，管理节点的 kubelet 需要登陆工作节点进行操作。
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

#### 安装docker

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
  ]
}
```
* 重启docker
```
systemctl daemon-reload
systemctl restart docker
```
