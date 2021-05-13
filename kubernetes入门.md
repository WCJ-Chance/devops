### 关闭CentOS自带防火墙服务
`# systemctl disable firewalld`
`# systemctl stop firewalld`
### 安装etcd和Kubernetes软件（会自动安装Docker软件）
`# yum install -y etcd kubernetes`
### 按顺序启动所有的服务
```
   # systemctl start etcd
   # systemctl start docker
   # systemctl start kube-apiserver
   # systemctl start kube-controller-manager
   # systemctl start kube-scheduler
   # systemctl start kubelet
   # systemctl start kube-proxy
```