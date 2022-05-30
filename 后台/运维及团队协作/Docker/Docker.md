# Docker

---

## 1、Docker概述

### 1-1.Docker为什么出现



### 1-2.Docker历史



### 1-3.Docker能干什么

## 2、Docker安装

> 多看文档，docker 官方文档
>
> https://docs.docker.com/engine/install/centos/
>
> 

### 2-0.前提条件

```shell
1.内核在>=3
uname -r
2.centos 7及以上
cat /etc/os-realease
```

### 2-1.卸载old docker

```shell
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```



### 2-2.设置repo

```shell
yum install -y yum-utils

docker国外仓库，非常慢！
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
使用阿里的镜像    
yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

### 2-3.安装docker引擎

```shell
yum install docker-ce docker-ce-cli containerd.io
```

### 2-4.启动docker

```shell
systemctl start docker
```

### 2-5.hello world

```shell
docker run hello-world
```

### 2-6.查看docker版本

```shell
docker version
```

### 2-7.docker已有的镜像

```shell
docker images
```

### 2-8.卸载docker

```shell
1.卸载 Docker 引擎、CLI 和 Containerd 软件包：
 sudo yum remove docker-ce docker-ce-cli containerd.io
 
2.主机上的映像、容器、卷或自定义配置文件不会自动删除。要删除所有映像、容器和卷：
 sudo rm -rf /var/lib/docker
 sudo rm -rf /var/lib/containerd
```

