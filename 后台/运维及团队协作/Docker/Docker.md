# Docker

---

## 1、Docker概述

### 1-1.Docker为什么出现

> 在以往开发中，出现了在我的电脑上可以运行，但是在服务器环境上却不可以的情况，说明两者的环境有差异，从而导致了不可用，
>
> 那是不是可以把环境也打包部署上去呢？就是我们的[jar+(redis+mysql+jdk+es)]整个环境都是包的组成部分。
>
> Docker就是做这个的！
>
> 传统：开发提供jar，运维负责部署以及环境准备
>
> 现在：开发提供的包中包含了jar+环境，一套操作全部搞定。

![image-20220616224154562](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220616224154562.png)



### 1-2.两种带环境安装软件的方案

> 开发者常常会说："它在我的机器可以跑了"（It works on my machine），言下之意就是，其他机器很可能跑不了。
>
> 环境配置如此麻烦，换一台机器，就要重来一次，旷日费时。很多人想到，能不能从根本上解决问题，软件可以带环境安装？也就是说，安装的时候，把原始环境一模一样地复制过来。
>
> 以下是两种方案：
>
> - 虚拟机
> - LXC

#### 1-2-1.虚拟机

> 虚拟机（virtual machine）就是带环境安装的一种解决方案。它可以在一种操作系统里面运行另一种操作系统，比如在 Windows  系统里面运行 Linux  系统。应用程序对此毫无感知，因为虚拟机看上去跟真实系统一模一样，而对于底层系统来说，虚拟机就是一个普通文件，不需要了就删掉，对其他部分毫无影响。
>
> 虽然用户可以通过虚拟机还原软件的原始环境。但是，这个方案有几个缺点。

![image-20220619093118015](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220619093118015.png)

**缺点：**

1. **资源占用多**

   虚拟机会独占一部分内存和硬盘空间。它运行的时候，其他程序就不能使用这些资源了。哪怕虚拟机里面的应用程序，真正使用的内存只有 1MB，虚拟机依然需要几百 MB 的内存才能运行。

2. **冗余步骤多**

   虚拟机是完整的操作系统，一些系统级别的操作步骤，往往无法跳过，比如用户登录。

3. **启动慢**

   启动操作系统需要多久，启动虚拟机就需要多久。可能要等几分钟，应用程序才能真正运行。

#### 1-2-2.Linux容器

> 由于虚拟机存在这些缺点，Linux 发展出了另一种虚拟化技术：Linux 容器（Linux Containers，缩写为 LXC）。
>
> **Linux 容器不是模拟一个完整的操作系统，而是对进程进行隔离。**或者说，在正常进程的外面套了一个[保护层](https://opensource.com/article/18/1/history-low-level-container-runtimes)。对于容器里面的进程来说，它接触到的各种资源都是虚拟的，从而实现与底层系统的隔离。
>
> 由于容器是进程级别的，相比虚拟机有很多优势。

![image-20220619093214236](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220619093214236.png)

**优点：**

1. **启动快**

   容器里面的应用，直接就是底层系统的一个进程，而不是虚拟机内部的进程。所以，启动容器相当于启动本机的一个进程，而不是启动一个操作系统，速度就快很多。

2. **资源占用少**

   容器只占用需要的资源，不占用那些没有用到的资源；虚拟机由于是完整的操作系统，不可避免要占用所有资源。另外，多个容器可以共享资源，虚拟机都是独享资源。

3. **体积小**

   容器只要包含用到的组件即可，而虚拟机是整个操作系统的打包，所以容器文件比虚拟机文件要小很多。

   总之，容器有点像轻量级的虚拟机，能够提供虚拟化的环境，但是成本开销小得多。

#### 1-2-3.两者比较

|          | 虚拟机                         | LXC容器化              |
| -------- | ------------------------------ | ---------------------- |
| 启动速度 | 慢(启动整个系统)               | 快(相当于启动某个进程) |
| 资源占用 | 多(即使不用也会占用分配的资源) | 少(共享资源)           |
| 体积大小 | 大(需要打包整个系统)           | 小(只打包用到的组件)   |

### 1-3.Docker是什么

> **Docker 属于 Linux 容器的一种封装，提供简单易用的容器使用接口。**它是目前最流行的 Linux 容器解决方案。

Docker 将应用程序与该程序的依赖，打包在一个文件里面。运行这个文件，就会生成一个虚拟容器。程序在这个虚拟容器里运行，就好像在真实的物理机上运行一样。有了 Docker，就不用担心环境问题。

总体来说，Docker 的接口相当简单，用户可以方便地创建和使用容器，把自己的应用放入容器。容器还可以进行版本管理、复制、分享、修改，就像管理普通的代码一样。

### 1-4.Docker能干什么

**应用更快速的交付和部署：**

​	传统：一堆帮助文档，安装程序，运维累的要死。

​	docker：打包镜像发布测试，一键运行

**更便捷的升级和扩缩容：**

​	使用了docker之后我们部署应用就像是搭积木。项目打包为一个镜像，扩展服务A，服务B

**更简单的系统运维：**

​	容器化之后，我们的开发环境，测试环境是高度一致的。

**更高效的资源利用：**

​	Docker是内核级别的虚拟化，可以在一个虚拟机上运行多个容器实例！服务器的性能被压榨到极致。

## 2、Docker安装与卸载

> 多看文档，docker 官方文档
>
> https://docs.docker.com/engine/install/centos/

### 2-0.Docker的基本组成

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220619094012593.png" alt="image-20220619094012593" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220619090450373.png" alt="image-20220619090450373" style="zoom:50%;" />



**镜像（Image）：**

> ​	**Docker 把应用程序及其依赖，打包在 image 文件里面。**
>
> ​	只有通过这个文件，才能生成 Docker 容器。
>
> ​	image 文件可以看作是容器的模板。Docker 根据 image 文件生成容器的实例。同一个 image 文件，可以生成多个同时运行的容器实例。
>
> ​	image 是二进制文件。实际开发中，一个 image 文件往往通过继承另一个 image 文件，加上一些个性化设置而生成。举例来说，你可以在 Ubuntu 的 image 基础上，往里面加入 Apache 服务器，形成你的 image。

```bash
# 列出本机的所有 image 文件。
$ docker image ls

# 删除 image 文件
$ docker image rm [imageName]
```

​	image 文件是通用的，一台机器的 image 文件拷贝到另一台机器，照样可以使用。一般来说，为了节省时间，我们应该尽量使用别人制作好的 image 文件，而不是自己制作。即使要定制，也应该基于别人的 image 文件进行加工，而不是从零开始制作。

​	为了方便共享，image 文件制作完成后，可以上传到网上的仓库。Docker 的官方仓库 [Docker Hub](https://hub.docker.com/) 是最重要、最常用的 image 仓库。此外，出售自己制作的 image 文件也是可以的



**容器(Container):**

> Docker通过容器技术，独立运行一个或一组应用。



**仓库(Repository):**

> Docker仓库就是存放镜像的地方。
>
> 私有仓库和公开仓库
>
> docker hub：公有仓库。



### 2-0.前提条件

```shell
1.内核在>=3
[root@tech1 ~]# uname -r
3.10.0-1062.el7.x86_64

2.centos 7及以上
[root@tech1 ~]# cat /etc/redhat-release	
CentOS Linux release 7.7.1908 (Core)
```

Docker 需要用户具有 sudo 权限，为了避免每次命令都输入`sudo`，可以把用户加入 Docker 用户组（[官方文档](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user)）。

（因为我本地是root用户所以就不需要这一步了。）

```bash
$ sudo usermod -aG docker $USER
```

### 2-1.卸载old docker

```shell
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```



### 2-2.设置repo

> Install the `yum-utils` package (which provides the `yum-config-manager` utility) 
>
> and set up the repository

```shell
1.安装工具包
yum install -y yum-utils

2.设置docker仓库
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
docker-ce ce:社区版  ee:企业版
$ sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

### 2-4.启动docker

> Docker 是服务器----客户端架构。命令行运行`docker`命令的时候，需要本机有 Docker 服务。如果这项服务没有启动，可以用下面的命令启动（[官方文档](https://docs.docker.com/config/daemon/systemd/)）。

```shell
两种启动docker的方式
# service 命令的用法
$ sudo service docker start

# systemctl 命令的用法
$ sudo systemctl start docker

启动之后，查看是否成功：
$ docker version
或
$ docker info
```

### 2-5.hello world

```shell
$ docker run hello-world
```

### 2-6.查看docker版本

```shell
$ docker version
```

### 2-7.docker已有的镜像

```shell
$ docker images
```

### 2-8.卸载docker

```shell
1.卸载 Docker 引擎、CLI 和 Containerd 软件包：
$ sudo yum remove docker-ce docker-ce-cli containerd.io
 
2.主机上的映像、容器、卷或自定义配置文件不会自动删除。要删除所有映像、容器和卷：
$ sudo rm -rf /var/lib/docker
$ sudo rm -rf /var/lib/containerd
```

## 3.阿里云镜像加速

> 可参考：
>
> https://help.aliyun.com/document_detail/60750.html

## 4.Docker运行流程

> 当我们执行docker run hello-world时发生了什么。

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220619210939264.png" alt="image-20220619210939264" style="zoom:50%;" />

## 5.Docker常用命令

### 5-1.帮助命令

```shell
$ docker version docker版本信息
$ docker info    docker系统信息
$ docker 命令 --help
```

### 5-2.镜像命令

#### 5-2-1.docker images

```shell
$ docker images --help

# 列出所有镜像
[root@tech1 ~]# docker images -a   
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    feb5d9fea6a5   9 months ago   13.3kB
# 解释
REPOSITORY	:仓库源
TAG			:标签
IMAGE ID	:镜像ID
CREATED		:创建时间
SIZE 		:镜像大小

[root@tech1 ~]# docker images --help

Usage:  docker images [OPTIONS] [REPOSITORY[:TAG]]

List images

Options:
  -a, --all             Show all images (default hides intermediate images)
  -q, --quiet           Only show image IDs
  
$ docker images -qa  // 列出所有镜像的image id
```

#### 5-2-2.docker search

```shell
$ docker search --help

# 从docker hub中搜索与mysql镜像
$ docker search mysql

# 从docker hub中搜索star数量大于等于3000的mysql镜像
$ docker search --filter-STARS=3000 mysql

[root@tech1 ~]# docker search --filter=STARS=4000 mysql
NAME      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql     MySQL is a widely used, open-source relation…   12755     [OK]       
mariadb   MariaDB Server is a high performing open sou…   4896      [OK]  
```

#### 5-2-3.docker pull

```shell
# 下载镜像
$ docker pull --help

# 从docker hub下载mysql镜像
[root@tech1 ~]# docker pull mysql
Using default tag: latest				# 默认下载最新版本
latest: Pulling from library/mysql
c1ad9731b2c7: Pull complete 			# 分层下载，  联合文件系统
54f6eb0ee84d: Pull complete 
cffcf8691bc5: Pull complete 
89a783b5ac8a: Pull complete 
6a8393c7be5f: Pull complete 
af768d0b181e: Pull complete 
810d6aaaf54a: Pull complete 
2e014a8ae4c9: Pull complete 
a821425a3341: Pull complete 
3a10c2652132: Pull complete 
4419638feac4: Pull complete 
681aeed97dfe: Pull complete 
Digest: sha256:548da4c67fd8a71908f17c308b8ddb098acf5191d3d7694e56801c6a8b2072cc # 签名，防伪作用
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest			# 真实下载地址

$ docker pull mysql 
$ docker pull docker.io/library/mysql:latest
上面这两个命令是等价的。


[root@tech1 ~]# docker pull --help
Usage:  docker pull [OPTIONS] NAME[:TAG|@DIGEST]  eg：$ docker pull mysql:5.7
Pull an image or a repository from a registry
Options:
  -a, --all-tags                Download all tagged images in the repository
      --disable-content-trust   Skip image verification (default true)
      --platform string         Set platform if server is multi-platform capable
  -q, --quiet                   Suppress verbose output
  
 # 指定版本下载镜像 当然，指定的版本必须存在，可以去docker hub的网站进行查询
 $ docker pull mysql:5.7 
```

**下例为docker hub中查看支持的mysql的版本：**

<img src="C:\Users\97146\AppData\Roaming\Typora\typora-user-images\image-20220621223313234.png" alt="image-20220621223313234" style="zoom:57%;" />

#### 5-2-4.docker rmi

> 删除镜像 rmi ： i代表image
>
> docker rm ：删除docker container
>
> 
>
> You can remove an image using its short or long ID, its tag, or its digest. 

```shell
# 按照image id删除镜像
$ docker rmi [IMAGE ID]

# 删除latest的redis，根据名称remove
$ docker rmi redis

#指定某个镜像的tag删除
$ docker rmi redis:6.2

# 根据镜像id删除
$ docker rmi image_id
# 删除多个镜像，根据image id
$ docker rmi image_id1 image_id2 image_id3
# 把docker images -qa查出来的所有镜像都删除掉
$ docker rmi -f $(docker images -qa)

# repository+tag的方式删除
$ docker rmi test2:latest
```





### 5-3.容器命令
