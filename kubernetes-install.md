[TOC]
# 更新yum组件,并安装wget
```
sudo yum update -y
sudo yum install -y net-tools
sudo yum install -y wget
```

# 修改host  将本机ip指向hostname
```
sudo hostnamectl set-hostname master
echo "127.0.0.1   $(hostname)" >> /etc/hosts
```
# 安装docker
```
yum remove docker docker-client docker-client-latest docker-common  docker-latest docker-latest-logrotate docker-logrotate docker-engine
sudo yum install -y yum-utils device-mapper-persistent-data  lvm2
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install -y docker-ce docker-ce-cli containerd.io
```
# 配置阿里云加速
```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://jhzi2d33.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl enable docker
sudo systemctl restart docker
```
# 关闭防火墙
```
sudo systemctl stop firewalld
sudo systemctl disable firewalld
```

# 关闭selinux
```
sudo setenforce 0
sudo sed -i "s/SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config
```
# 关闭swap，禁用交换分区
```
sudo swapoff -a
sudo yes | cp /etc/fstab /etc/fstab_bak
sudo cat /etc/fstab_bak |grep -v swap > /etc/fstab
```

# 安装kubelet kubeadm kubectl(版本可以替换)
```
sudo yum install -y kubelet-1.16.1 kubeadm-1.16.1 kubectl-1.16.1 --disableexcludes=kubernetes
sudo systemctl restart docker
sudo systemctl enable kubelet && systemctl start kubelet
```

# 修改网络配置
```
cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sudo sysctl --system
```
#初始化Master
```
sudo kubeadm config print init-defaults > kubeadm-init.yaml

cat <<EOF >  ./kubeadm-init.yaml
apiVersion: kubeadm.k8s.io/v1beta2
bootstrapTokens:
- groups:
  - system:bootstrappers:kubeadm:default-node-token
  token: abcdef.0123456789abcdef
  ttl: 24h0m0s
  usages:
  - signing
  - authentication
kind: InitConfiguration
localAPIEndpoint:
#改成自己得ip
  advertiseAddress: 192.168.10.210 
  bindPort: 6443
nodeRegistration:
  criSocket: /var/run/dockershim.sock
  name: master
  taints:
  - effect: NoSchedule
    key: node-role.kubernetes.io/master
---
apiServer:
  timeoutForControlPlane: 4m0s
apiVersion: kubeadm.k8s.io/v1beta2
certificatesDir: /etc/kubernetes/pki
clusterName: kubernetes
controllerManager: {}
dns:
  type: CoreDNS
etcd:
  local:
    dataDir: /var/lib/etcd
#镜像源
imageRepository: registry.cn-hangzhou.aliyuncs.com/google_containers 
kind: ClusterConfiguration
kubernetesVersion: v1.16.0
networking:
  dnsDomain: cluster.local
  serviceSubnet: 10.96.0.0/12
scheduler: {}
EOF
```

# 下载镜像

`sudo kubeadm config images pull --config kubeadm-init.yaml`

# 初始化
```
sudo kubeadm init --config kubeadm-init.yaml
mkdir -p $HOME/.kube
sudo cp -rf /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

# 配置网络
```
wget https://docs.projectcalico.org/v3.8/manifests/calico.yaml
#执行此命令查看ip，将calico.yaml得ip 改成这个
cat kubeadm-init.yaml | grep serviceSubnet:
```

# 初始化网络
```
kubectl apply -f calico.yaml
```
# 配置单节点模式
```
kubectl taint nodes --all node-role.kubernetes.io/master-
```

# 执行如下命令，等待 3-10 分钟，直到所有的容器组处于 Running 状态
```
watch kubectl get pod -n kube-system -o wide
```

# 查看 master 节点初始化结果
```
kubectl get nodes -o wide
```