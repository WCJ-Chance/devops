`uname -r //查看内核版本 >=3.10即可`

`yum -y update // 升级所有包同时也升级软件和系统内核 yum -y upgrade 是只升级包`

`yum remove docker docker-common docker-selinux docker-engine // 卸载旧版本`

`yum install -y yum-utils device-mapper-persistent-data lvm2 //安装需要的软件包 yum-util提供yum-config-manager功能，另两个是devicemapper驱动依赖`

```
   · yum-config-manager --add-repo http://download.docker.com/linux/centos/docker-ce.repo（中央仓库）
   · yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo（阿里仓库）
```

```
   yum list docker-ce --showduplicates | sort -r //查看可用版本有哪些
```

```
   yum install docker-ce-版本号 //选择一个版本安装
```

```
   systemctl start docker  // 启动docker
   systemctl enable docker // 设置开机自启
```

### docker设置国内镜像源
```
   cat /etc/docker/daemon.json // 查看（无此文件则未配置）
   vim /etc/docker/daemon.json

   daemon.json: 
      {
        "registry-mirrors":["https://registry.docker-cn.com"]
      }
   
   systemctl restart docker //重启
   docker info | grep Mirrors -A 1 // 查看镜像源是否更改成功
```
