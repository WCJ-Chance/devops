## 1.1 什么是Kubernetes
· 基于容器技术的分布式架构领先方案；谷歌十几年以来大规模应用容器技术的经验积累和升华的一个重要成果---Borg的一个开源版本；Borg是谷歌内部使用的大规模集群管理系统，基于容器技术，目的是实现资源管理的自动化，以及跨多个数据中心的资源利用率的最大化。
· 如果我们的系统设计遵循了k8s的设计思想，那么传统系统架构中那些和业务没有多大关系的底层代码或功能模块，都可以立刻从我们的视线中消失，不必再费心于负载均衡器的选型和部署实施问题，不必再考虑引入或自己开发一个复杂的服务治理框架，不必再头疼于服务监控和故障处理模块的开发。
· k8s是一个开放的开发平台，与J2EE不同，它不局限于任何一种语言，没有限定任何编程接口，所以不论是用Java、Go、C++还是用Python编写的服务，都可以毫无困难地映射为K8s的Service，并通过标准的TCP通信协议进行交互；由于K8s对现有的编程语言、编程框架、中间件没有任何侵入性，现有的系统也很容易改造升级并迁移到K8s平台上。
· K8s是一个完备的分布式系统支撑平台。K8S具有完备的集群管理能力，包括多层次的安全防护和准入机制、多租户应用支撑能力、透明的服务注册和服务发现机制、内建智能负载均衡器、强大的故障发现和自我修复能力、服务滚动升级和在线扩容能力、可扩展的资源自动调度机制，以及多粒度的资源配额管理能力。

## 环境准备

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

## 启动MySQL服务

### 为MySQL服务创建一个RC定义文件
mysql-rc.yaml:
```
    apiVersion: v1
    kind: ReplicationController             # kind:此资源对象的类型->副本控制器RC
    metadata:
      name: mysql                           # RC的名称，全局唯一
    spec:                                   # spec：RC的相关属性定义
      replicas: 1                           # Pod副本期待数量
      selector:
        app: mysql                          # 符合目标的Pod拥有此标签
      template:                             # 根据此模板创建Pod的副本（实例）
        metadata:
          labels:
            app: mysql                      # Pod副本拥有的标签，对应RC的Selector
        spec:
          containers:                       # Pod内容器的定义部分
          - name: mysql                     # 容器的名称
            image: mysql                    # 容器对应的Docker Image
            ports:
            - containerPort: 3306           # 容器应用监听的端口
            env:                            # 注入容器内的环境变量
            - name: MYSQL_ROOT_PASSWORD
              value: "123456"
```
- spec.selector是RC的Pod标签(Label)选择器，即监控和管理拥有这些标签的Pod实例，确保当前集群上始终有且仅有replicas个Pod实例在运行，这里我们设置replicas=1表示只能运行一个MySQL Pod实例。
- 当集群中运行的Pod数量小于replicas时，RC会根据spec.template一节中定义的Pod模板来生成一个新的Pod实例，spec.template.metadata.labels指定了该Pod的标签，需要特别注意的是：这里的labels必须匹配之前的spec.selector,否则此RC每次创建了一个无法匹配Label的Pod，就会不停地尝试创建新的Pod，最终陷入“为他人作嫁衣”的悲惨世界中。

### 发布yaml
```
   # kubectl create -f mysql-rc.yaml
   replicationcontroller "mysql" created
```

### 查看RC
```
   # kubectl get rc
   NAME      DESIRED   CURRENT   READY     AGE
   mysql     1         1         0         6s
```

### 查看Pod的创建情况
```
   # kubectl get pods

```