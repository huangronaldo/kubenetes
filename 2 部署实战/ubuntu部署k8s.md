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
* 关闭swap，k8s要求系统需关闭swap，以便性能达到最大
```
# 第一步：直接关闭swap
swapoff -a
# 修改/etc/fstab注释配置信息，重启生效
vim /etc/fstab
```
* SSH免密登录
```
ssh-keygen  -t rsa
ssh-copy-id -p22 -i ~/.ssh/id_rsa.pub root@172.30.77.215
```
