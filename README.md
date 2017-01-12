# k8s

## Kubernetes Kubeadm安装

### Hostname

```
hostnamectl --static set-hostname xxxx
```

### 安装组件

```shell
# 添加 yum 源
tee /etc/yum.repos.d/mritd.repo << EOF
[mritdrepo]
name=Mritd Repository
baseurl=https://yum.mritd.me/centos/7/x86_64
enabled=1
gpgcheck=1
gpgkey=https://cdn.mritd.me/keys/rpm.public.key
EOF

# 刷新cache
yum makecache
# 安装
setenforce 0
yum install -y docker kubelet kubeadm kubectl kubernetes-cni
systemctl enable docker && systemctl start docker
systemctl enable kubelet && systemctl start kubelet
```


### 初始化Master

```
kubeadm init --use-kubernetes-version v1.5.1
```
如果使用 flannel 网络可以通过参数 `--pod-network-cidr=10.244.0.0/16` 指定子网分配规则

### 结点加入集群

```
kubeadm join --token=c96927.4122f2947ec7a7a3 k1
```

### 安装网络

任意选择一个网络组件
https://kubernetes.io/docs/admin/addons/


### Docker Images
依赖Docker Images官方克隆，包括Dashboard监控依赖组件heapster。
使用方法

```shell
images=(kube-proxy-amd64:v1.5.1 kube-discovery-amd64:1.0 kubedns-amd64:1.9 kube-scheduler-amd64:v1.5.1 kube-controller-manager-amd64:v1.5.1 kube-apiserver-amd64:v1.5.1 etcd-amd64:3.0.14-kubeadm kube-dnsmasq-amd64:1.4 exechealthz-amd64:1.2 pause-amd64:3.0 kubernetes-dashboard-amd64:v1.5.0 dnsmasq-metrics-amd64:1.0 heapster-grafana:v2.6.0-2 masir/heapster:v1.3.0-beta.0)
for imageName in ${images[@]} ; do
  docker pull masir/$imageName
  docker tag masir/$imageName gcr.io/google_containers/$imageName
  docker rmi masir/$imageName
done
```

