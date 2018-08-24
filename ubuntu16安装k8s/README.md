ubuntu16安装k8s
====

准备
----
1. 更新apt-get
```apt-get update```
2. 配置docker源
```vi /etc/apt/sources.list.d/docker.list```
```
deb http://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse
```
3. 配置k8s源
```curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add```
```vi /etc/apt/sources.list.d/k8s.list```
```deb http://apt.kubernetes.io/ kubernetes-xenial main```
4. ```apt-get update```
5. 关闭swap ```swapoff -a```
____

安装docker
----
```apt-get install docker.io```
____

安装k8s
----

```apt-get install kubeadm=1.9.0-00
apt-get install kubelet=1.9.0-00
apt-get install kubectl=1.9.0-00 ```
____

配置master
----

1. 初始化
```kubeadm init –pod-network-cidr 10.244.0.0/16```
2. 配置kubectl
```cp /etc/kubernetes/admin.conf ~/.kube/config
chown 0:0 ~/.kube/config```
3. 把初始化后返回的"kubeadm join --token xxxxxxxx" 记录下来，加入node时需要
4. 配置CNI
```mkdir -p /etc/cni/net.d/```
```vi /etc/cni/net.d/10-mycnf.conf```
```
{
	"cniVersion": "0.3.0",
	"name": "mynet",
	"type": "bridge",
	"bridge": "cni0",
	"isGateway": true,
	"ipMasq": true,
	"ipam": {
		"type": "host-local",
		"subnet": "10.244.0.0/16",
		"routes": [
			{ "dst": "0.0.0.0/0" }
		]
	}
}
```
____

配置node
----

1. 同上，安装docker安装k8s
2. 执行kubeadm join --token xxxxxx ....
3. 同上，配置CNI
____

问题与解决
----