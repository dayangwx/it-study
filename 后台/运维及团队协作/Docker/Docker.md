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

## 3、阿里云镜像加速

> 可参考：
>
> https://help.aliyun.com/document_detail/60750.html

## 4、Docker运行流程

> 当我们执行docker run hello-world时发生了什么。

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220619210939264.png" alt="image-20220619210939264" style="zoom:50%;" />

## 5、Docker常用命令

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

> 容器是建立在镜像的基础上的，有镜像才能启动容器。

#### 5-3-1.启动容器

```bash
$ docker run [可选参数] image
# 参数说明
--name="Name" 容器名字 
-d 			  后台静默运行  类似于：nohup
-it			  容器的 Shell 映射到当前的 Shell，然后你在本机窗口输入的命令，就会传入容器。
-p		      指定容器的端口，-p 8080:8080  第一个是本机的端口，第二个是容器的端口
	-p ip:主机端口:容器端口
	-p 主机端口:容器端口 (常用)
	-p 容器端口
-P    		  随机指定端口


1:下载centos 镜像
$ docker pull centos
2:启动docker 
$ docker run -it centos /bin/bash
-it参数   ：容器的 Shell 映射到当前的 Shell，然后你在本机窗口输入的命令，就会传入容器。
/bin/bash：容器启动以后，内部第一个执行的命令。这里是启动 Bash，保证用户可以使用 Shell。

#eg：
# 启动后会进入到容器内部，可以发现你的用户已经发生了变化。
# 退出使用exit，发现我们又回到了tech1用户，说明从容器中出来了。
[root@tech1 ~]# docker run -it centos /bin/bash
[root@b7e5d596f3ab /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@b7e5d596f3ab /]# exit
[root@b7e5d596f3ab /]# exit
exit
[root@tech1 ~]# 
```

#### 5-3-2.查看运行的容器

```shell
$ docker ps 
-a			all，列出所有的容器，包括正在运行的以及已经停止的 
-q			只列出容器的id
-n=?		个数过滤，比如说-n=2，就是列出两个
-s			status,查看状态

# 查看当前正在运行的容器
$ docker ps 

# 查看正在运行的以及曾经运行过的容器
$ docker ps -a

# 列出所有的容器的两个id
$ docker ps -aq -n=2
```

#### 5-3-3.退出容器

```shell
# 在进入容器后，退出的时候会停止容器，执行:
$ exit

# 退出容器的时候不停止容器
$ ctrl + P +Q
```

#### 5-3-4.停止容器

> docker stop 与 docker kill 均可以将容器停掉，但二者究竟有什么区别呢？首先，摘录一下官网对这两个功能的描述：
>
> - **docker stop**: *Stop a running container (**send SIGTERM, and then SIGKILL after grace period**) [...] The main process inside the container will receive SIGTERM, and after a grace period, SIGKILL. [emphasis mine]*
> - **docker kill**: *Kill a running container (**send SIGKILL, or specified signal**) [...] The main process inside the container will be sent SIGKILL, or  any signal specified with option --signal. [emphasis mine]*
>
> 　docker stop，支持“优雅退出”。先发送SIGTERM信号，在一段时间之后（10s）再发送SIGKILL信号。Docker内部的应用程序可以接收SIGTERM信号，然后做一些“退出前工作”，比如保存状态、处理当前请求等。
>
> 
> 　docker kill，发送SIGKILL信号，应用程序直接退出。
>
>
> 　线上应用优雅退出十分必要。docker stop也不是docker独有的设计，lxc和google borg系统都有类似设计，即在发送SIGKILL之前，发送SIGTERM信号通知任务。

```shell
$ docker stop 
	 -t, --time int   Seconds to wait for stop before killing it (default 10)

# 停止正在运行的所有docker，下面两个作用相同
$ docker stop $(docker ps -aq)
$ docker ps -aq | xargs docker stop

$ docker kill containerId
```

#### 5-3-5.删除容器

> 容器停止运行之后，并不会消失，用下面的命令删除容器文件

```shell
# 列出所有的容器，包括正在运行的以及已经停止的
$ docker container ls --all
$ docker ps -a

# 删除全部容器
$ docker rm $(docker ps -aq)


# 注意：如果一个命名的容器启动后又停止了，再次用同样的命名启动是启动不起来的。
# 例如：下例中centosLeoLiu2这个名字已经被使用过了。
[root@tech1 ~]# docker run --name="centosLeoLiu2" -it centos /bin/bash
docker: Error response from daemon: Conflict. The container name "/centosLeoLiu2" is already in use by container "9c3605bd3a32f06e3d5acc1c204a45bcd4b0b66ed964b0d2873d670bf9f44928". You have to remove (or rename) that container to be able to reuse that name.
See 'docker run --help'
# 可以使用一下两个命令启动或重启：
$ docker start container_id
$ docker restart container_id
# 但是上述起来后无法进入容器，我们使用一下命令进入容器：
$ docker container exec -it container_id /bin/bash
```

### 5-4.其他常用命令

> # 查看资源占用情况
>
> docker stats

#### 5-4-1.后台启动容器

```shell
$ docker run -d image_name
# 后台静默启动，用docker ps看发现没有启动，
# 坑：docker后台静默启动一定要有个前台进程，docker发现没有应用，就自动停止了。
```

#### 5-4-2.查看日志

```shell
$ docker logs --help
Usage:  docker logs [OPTIONS] CONTAINER

Fetch the logs of a container

Options:
      --details        Show extra details provided to logs
  -f, --follow         Follow log output
      --since string   Show logs since timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes)
  -n, --tail string    Number of lines to show from the end of the logs (default "all")
  -t, --timestamps     Show timestamps
      --until string   Show logs before a timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes)

# 查看容器最后30行并显示时间，并且follow，实时动态监听
$ docker logs -tf --tail 30 container_id
```

#### 5-4-3.查看容器的进程信息

> 可以用于后期杀死进程

```shell
[root@tech1 ~]# docker top $(docker ps -q)
UID                 PID                 PPID                C                   STIME               TTY 
root                90252               90232               0                   21:33               ?
root                91348               90232               0                   21:50               ?   
```

#### 5-4-4.查看容器元数据

> 有镜像的基本信息如id，name，创建时间，状态等，也有镜像信息，挂载，配置，网络等等信息，非常全面！

```shell
$ docker inspect container_id
```

#### 5-4-5.进入正在运行的容器

> 多数情况我们的容器是后台运行的，所以重新进入容器就显的很重要。

##### 5-4-5-1.exec

> 进入容器后开启一个新的终端

```shell
$ docker exec -it container_id /bin/bash
```

##### 5-4-5-2.attach

> 进入一个正在运行的容器

```shell
$ docker attach container_id
```

#### 5-4-6.从容器内拷贝文件到主机

> 把容器内部的文件拷贝出来到主机上。
>
> 这里不需要容器必须是正在运行的，因为就算是不运行，只要容器没有被删除文件就还是存在的。
>
> 如果是把主机的文件拷贝到容器上我们常用**挂载**。

```bash
# 启动一个容器并进入
$ docker run -it centos /bin/bash
# 在容器内的home目录下创建一个文件
$ touch test.java
# 此时你可以选择停止容器也可以选择不停止，本案例选择停止
$ exit
# 回到本机环境中，将centos容器的/home/test.java文件复制到本机的/home目录下
$ docker cp container_id:/home/test.java /home
```

**拷贝是个手动的过程，后续采用卷的技术将自动完成文件的拷贝。**

### 5-5.小结

<img src="C:\Users\97146\AppData\Roaming\Typora\typora-user-images\image-20220628221456312.png" alt="image-20220628221456312" style="zoom:67%;" />

```bash
常用命令合集
```

## 6、牛刀小试

### 6-1.Docker部署nginx

> 使用docker部署nginx并可以通过windows访问ng。

```bash
# 1.拉取镜像
$ docker pull nginx:tag
# 2.启动ng
$ docker run -d --name nginx01 -p 3344:80 nginx:tag
# -d 	: 后台静默运行
# --name: 起名字
# -p 3344:80 : 3344为宿主机的端口 80为ng的端口 两者做映射，能够在通过3344访问到80
# 3.进入ng进行查看修改等操作
$ docker exec -it nginx02 /bin/bash
以上三步完成(或前两步)，我们就可以通过curl localhost:3344，或者在windows里ip:80访问到ng了。
```

![image-20220628224645966](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220628224645966.png)

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220628224729086.png" alt="image-20220628224729086" style="zoom:67%;" />

**思考：**

​	如果我现在需要改动ng的配置，那么以目前的情况来看，我必须进入到容器内部才能更改。

​	那么我们是否可以在容器外部提供一个映射路径，实现在宿主机修改配置使容器中的ng配置生效？

​	**数据卷！！**

### 6-2.Docker部署tomcat

> 使用docker部署nginx并可以通过windows访问tomcat。

```shell
# 1.拉取镜像
$ docker pull tomcat 
# 2.启动容器
$ docker run -d --name tomcat01 -p 8888:8080 tomcat
# 3.访问
发现404，
进入webapps目录发现为空，
因为docker是以最小文件去运行的，
解决方案：
进入webapp.dist目录，将这里的文件全部复制到webapp目录下
$ docker exec -it tomcat01 /bin/bash
$ cp -r webapps.dist/* webapps
重新访问，发现可以了！
```

![image-20220629230543656](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220629230543656.png)

**思考：**

​	如果我现在需要改动tomcat的配置或者是部署一个web服务，那么以目前的情况来看，我必须进入到容器内部才能操作。

​	那么我们是否可以在容器外部提供一个映射路径，实现在宿主机修改能够映射到容器中？

​	

### 6-3.Docker部署ES

## 7、Docker界面可视化工具

### 7-1.portainer

> Docker图形化界面管理工具。

```shell
$ docker run -d -p 8088:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

启动成功后访问：

​	http://ip:8088

![image-20220704224346933](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220704224346933.png)

![image-20220704224147975](C:\Users\97146\AppData\Roaming\Typora\typora-user-images\image-20220704224147975.png)





### 7-2.Rancher(CI/CD)



## 8、Docker镜像详解

### 8-1.Docker镜像是什么



### 8-2.Docker镜像加载原理



### 8-3.分层理解



### 8-4.Commit镜像

> 我们把别人的镜像修改成我们自己想要的样子，然后把它制作成一个镜像。

```shell
# 启动一个tomcat
$ docker run -it -p 8080:8080 --rm tomcat /bin/bash

# 把webapp.dist下的文件全部拷贝到webapp目录下
$ docker exec -it container_id /bin/bash 
$ cp -r webapp.dist/* webapp

# docker commit
$ docker commit -a "leoliu" -m "add webapps application" 783adfb23787 tomcat-leo01:1.0

# 查看镜像
[root@tech1 ~]$ docker images
REPOSITORY            TAG       IMAGE ID       CREATED         SIZE
tomcat-leo01          1.0       71dd8e339d09   6 seconds ago   487MB
```

## 9、Docker volume(数据卷)

### 9-1.volume的概念

> 如果我们用docker启动了一个mysql，并且在使用的过程中保存进去了数据，
>
> 那么，如果我们停止了mysql，就会导致数据全部丢失，相当于是删库跑路了。
>
> 如何解决上述问题呢？
>
> Volume （数据卷），
>
> **其实就是把宿主机的目录与容器内的目录进行挂载，**
>
> **做到容器内目录下发生变化就会同步到宿主机上，**
>
> **而宿主机目录发生变化同样可以同步到容器内目录下，即使容器时停止的，只要它没有删除就可以。**
>
> 可以简单把这两个目录理解为**双向绑定**

```shell
$ docker run -v /home/testcentos:/home ...
```

**优点：**

​	**只需要操作宿主机就可以实现容器内的数据变化。**

### 9-2.实操1：centos

```shell
# 1.启动一个容器
$ docker run -it -v /home/testcentos:/home centos /bin/bash
						宿主机目录    : 容器目录
# 2.在容器内部给home目录写入文件
$ touch test.java

# 3.在宿主机目录下查看
$ cd /home/testcentos & ls

# 4.停止容器
$ exit

# 5.在宿主机目录下修改test.java文件并新增newfile.txt
$ vim test.java
$ touch newfile.txt

# 6.重启刚才的centos容器并进入
$ docker ps -a | grep centos
$ docker start container_id
$ docker attach container_id

# 7.进入到容器的/home目录下查看变化。
$ ls
[root@1a7681a42d01 home]$ ls
newfile.txt  test.java

# 8.查看容器详细信息
$ docker inspect container_id | grep -A 9 Mounts
[root@tech1 testcentos]$ docker inspect 1a7681a42d01 | grep -A 9 Mounts
        "Mounts": [
            {
                "Type": "bind",
                "Source": "/home/testcentos",  -- 宿主机地址
                "Destination": "/home",			-- 容器地址
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
```

### 9-3.实操2：mysql

> docker启动mysql并能够使用客户端连接以及创建表等保存数据。

```shell
# 启动msyql容器
-d : 后台启动
-v : 数据卷 宿主机:容器  可以多个-v
-e : 环境变量
--name: 起名
$ docker run -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql  -e MYSQL_ROOT_PASSWORD=123456 --name mysql-leo mysql:5.7 

验证一下：
telnet 192.168.18.127 3310
```

使用dataGrip连接并创建表和数据

![image-20220706222555742](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220706222555742.png)

**注：**

​	**即使我们删除了容器，数据也是不会丢失的。只要重启就可以了。（按照同样的启动命令）**

### 9-4.三种挂载方式

#### 9-4-1.匿名挂载

> 顾名思义，就是没有名字的挂载
>
> 格式：
> 	... -v 容器内路径

```shell
$ docker run -d -P -v /etc/nginx --name nginx01 nginx

# 查看所有的volume
$ docker volume ls
DRIVER    VOLUME NAME
local     2a56911f230706bdf7c054a207c1dc40afed0830ab5047416d119aa4052423a2
local     83111f5469b8615b829ebd06819df82ffb14a21b74ab82c54673aa34ef9fd917

# 查看某个指定volume的详情
$ docker volume inspect 83111f5469b8615b829ebd06819df82ffb14a21b74ab82c54673aa34ef9fd917
[
    {
        "CreatedAt": "2022-07-12T20:17:39+08:00",
        "Driver": "local",
        "Labels": null,
       -- 挂载的路径 "Mountpoint":"/var/lib/docker/volumes/2a56911f230706bdf7c054a207c1dc40afed0830ab5047416d119aa4052423a2/_data",
        "Name": "2a56911f230706bdf7c054a207c1dc40afed0830ab5047416d119aa4052423a2",
        "Options": null,
        "Scope": "local"
    }
]

```

#### 9-4-2.具名挂载

> 就是有名字的挂载。
>
> 格式：
> 	... -v 名字:容器内路径

```shell
$ docker run -d -P --name juming-nginx001  -v juming-ng:/etc/nginx nginx

# 查看所有的volume
$ docker volume ls
DRIVER    VOLUME NAME
local     2a56911f230706bdf7c054a207c1dc40afed0830ab5047416d119aa4052423a2
local     83111f5469b8615b829ebd06819df82ffb14a21b74ab82c54673aa34ef9fd917
local     juming-ng

# 查看某个指定volume的详情
$ docker volume inspect juming-ng
[
    {
        "CreatedAt": "2022-07-12T20:31:32+08:00",
        "Driver": "local",
        "Labels": null,
        -- 挂载路径，就是宿主机的路径
        "Mountpoint": "/var/lib/docker/volumes/juming-ng/_data",
        "Name": "juming-ng",
        "Options": null,
        "Scope": "local"
    }
]
```

#### 9-4-3.指定路径挂载

> 就是指定宿主机路径的挂载。
>
> 格式：
>
> ​	... -v 宿主机路径:容器内路径

```shell
$ docker run -d -P --name nginxleo05 -v /home/testnginx:/etc/nginx nginx这个为什么是错误的？

https://blog.csdn.net/xyajia/article/details/107161167
```

#### 9-4-4.拓展

> ro
>
> rw

```shell
$ docker run -d -P --name juming-nginx001  -v juming-ng:/etc/nginx:ro nginx
$ docker run -d -P --name juming-nginx001  -v juming-ng:/etc/nginx:rw nginx
ro: read only    只读
rw: read write   可读可写

只要看到ro就说明挂载的路径只能通过宿主机来修改，容器内是操作不了挂载的路径的！

```



### 9-5.初识Dockerfile

> 使用Dockerfile制作镜像并挂载

```shell
# 写一个脚本
vim DockerFile
From centos 

VOLUME ["volume01","volume02"]  ## 只写路径，说明是匿名挂载

CMD echo "------end------"
CMD /bin/bash

# build，生成镜像
$ docker build -f DockerFile -t leo/centos:01 .

# 查看镜像
$ docker images 

# 启动镜像
$ docker run -it leo/centos:01 /bash/shell

# 查看挂载路径
$ docker inspect container_id | grep -A 20 Mounts
 "Mounts": [
            {
                "Type": "volume",
                "Name": "1391672ed2226a9a63da677fec7bff575519f7fca93d940517a10e1ee8df016d",
                "Source": "/var/lib/docker/volumes/1391672ed2226a9a63da677fec7bff575519f7fca93d940517a10e1ee8df016d/_data",
                "Destination": "volume01",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            },
            {
                "Type": "volume",
                "Name": "f026e2286bbaf8c310f97efacee4cb6fdf3035a7d95eea2c653a7be9f337045d",
                "Source": "/var/lib/docker/volumes/f026e2286bbaf8c310f97efacee4cb6fdf3035a7d95eea2c653a7be9f337045d/_data",
                "Destination": "volume02",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
         ]
```

问题：

​	**多个容器之间如何数据共享？比如说：两个mysql之间如何互相对方的数据。**

### 9-6.数据容器卷

> 实现两个或多个容器之间数据共享。
>
> --volumes-from 

```shell
# 启动一个容器，当作father
$ docker run -it --name docker01 leo/centos:01 

# 启动另外一个容器，挂载到father上
$ docker run -it --volumes-from docker01 --name docker02 leo/centos:01
# 在这个容器中的volume1中新建个文件
touch docker02.txt
# 然后查看docker01中的volume01目录下是否有这个新建的文件，肯定是有的。

#再启动第三个容器
$ docker run -it --volumes-from docker01 --name docker03 leo/centos:01 
# 在这个容器中的volume1中新建个文件
touch docker03.txt
# 然后查看docker01和docker02中的volume01目录下是否有这个新建的文件，肯定是有的。

```

总结：

​	常用于容器之间配置信息的传递，数据卷容器的生命周期一直到没有容器使用为止。

​	不过如果持久化到本地了，那本地的数据是不会删除的。

​	所以你可以让father的持久化到本地。



两个mysql之间数据共享:

```shell
$ docker run -d -p 3310:3306 -v /etc/mysql/conf.d -v /var/lib/mysql  -e MYSQL_ROOT_PASSWORD=123456 --name mysql-leo mysql:5.7 

$ docker run -d -p 3310:3306 --volumes-from mysql-leo  -e MYSQL_ROOT_PASSWORD=123456 --name mysql-leo02 mysql:5.7 

```



## 10、Dockerfile

> dockerfile的作用就是用来构建镜像的一个文本文件！
>
> 也就是说他是用来制作镜像的！

### 10-1.Dokcerfile基本介绍

> Dockerfile是面向开发的，我们以后要发布项目、制作镜像，就需要编写Dockerfile文件。
>
> Docker镜像逐渐成为企业交付标准，必须掌握。把你写的springboot工程制作成一个镜像，而不是一个jar或者war了。如何制作？
>
> Dockerfile：构建文件，定义了一切的步骤，源代码。
>
> DockeImage：通过Dockerfile生成的镜像，是最终发布和运行的产品！
>
> Docker Container：容器就是镜像启动后提供服务的！

### 10-2.Dockerfile构建过程

- 编写Dockerfile文件
- docker build 生成镜像
- docker images 查看镜像
- docker run    启动镜像

### 10-3.Dockerfile常用命令

> 指令全用大写！

```shell
FROM			#基础镜像，一切从这里开始
MAINTAINER		#镜像是谁写的，姓名+邮箱
RUN				#镜像构建的时候需要运行的命令
ADD				#步骤：加tomcat镜像，就添加tomcat的压缩包
WORKDIR			#镜像的工作目录
VOLUME			#挂载的目录
EXPOSE			#暴露端口
CMD 			#指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT		#指定这个容器启动的时候要运行的命令，可以追加命令
ONBUILD			#当构建一个被继承Dockerfile，这个时候会运行ONBUILD的指令
COPY			#类似ADD，将我们的文件拷贝到镜像中
ENV				#构建的时候设置环境变量
```



<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220713204610577.png" alt="image-20220713204610577" style="zoom:47%;" />

### 10-4.牛刀小试

#### 10-4-1.制作自己的centos镜像

> 目标：
>
> ​	自己制作一个centos镜像，增加vim和ll以及ifconfig功能

```shell
# 1.编写Dockerfile脚本
root@tech1 Dockerfile]# cat Dockerfile-centos 
FROM centos
MAINTAINER Leo Liu<971465407@qq.com>
ENV MYPATH /usr/local
WORKDIR $MYPATH

# 用于解决yum install没有urls问题
RUN cd /etc/yum.repos.d/
RUN sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
RUN sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*

# 安装vim -y：安装过程中所有的yes/no问题都选yes
RUN yum install -y vim
RUN yum install -y net-tools

EXPOSE 80


CMD echo $MYPATH
CMD echo "-----end----”
CMD /bin/bash

# 2.build Dockerfile文件
$ docker build -f Dockerfile-centos -t centos-plus-leo .
打印截取：
Successfully built a89dd8cd0db2
Successfully tagged centos-plus-leo:latest

# 3.查看镜像
$ docker images
REPOSITORY            TAG       IMAGE ID       CREATED         SIZE
centos-plus-leo       latest    a89dd8cd0db2   4 minutes ago   326MB

# 4.启动镜像
$ docker run -it centos-plus-leo

启动之后，查看vim命令与ifconfig命令是否可以执行。
```

#### 10-4-2.CMD与ENTRYPOINT的区别

> CMD 			#指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代
> ENTRYPOINT	  #指定这个容器启动的时候要运行的命令，可以追加命令
>
> 
>
> **CMD不可以追加，但是ENTRYPOINT可以追加命令**！

```shell
# CMD 不可以在后面追加命令
$ vim Dockerfile-CMD
  FROM centos
  CMD ["ls","-a"]
$ docker build -f Dockerfile-CMD -t centos-CMD .

$ docker run centos-CMD 

$ docker run centos-CMD -l
docker: Error response from daemon: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: "-a": executable file not found in $PATH: unknown.

在后面追加-l会直接报错！
```

```shell
# ENTRYPOINT 可以在后面追加命令
$ vim Dockerfile-ENTRYPOINT
	FROM centos
	ENTRYPOINT ["ls","-a"]
$ docker build -f Dockerfile-ENTRYPOINT -t centos-ENTRYPOINT .

$ docker run centos-ENTRYPOINT
.
..
.dockerenv
bin
dev
etc
home
...

$ docker run centos-ENTRYPOINT -l
total 0
drwxr-xr-x.   1 root root   6 Jul 14 12:51 .
drwxr-xr-x.   1 root root   6 Jul 14 12:51 ..
-rwxr-xr-x.   1 root root   0 Jul 14 12:51 .dockerenv
lrwxrwxrwx.   1 root root   7 Nov  3  2020 bin -> usr/bin
drwxr-xr-x.   5 root root 340 Jul 14 12:51 dev
...

不仅没有报错，-l生效了。

```

#### 10-4-3.制作自己的tomcat镜像

> 需求：
>
> ​	1：制作tomcat镜像，包含jdk、tomcat
>
> ​	2：启动tomcat
>
> ​	3：挂载webapps路径，做到宿主机加入项目，tomcat可以运行它。
>
> ​	4：挂载日志catalina.out，保证我们可以通过宿主机看到日志

```shell
# 进入到/home/liuwx目录下编写Dockerfile文件
$ vim Dockerfile
FROM centos
MAINTAINER leo<971465407@qq.com>

COPY readme.txt /usr/local/readme.txt

ADD jdk-8u311-linux-x64.tar.gz /usr/local/
ADD apache-tomcat-9.0.64.tar.gz /usr/local/

RUN cd /etc/yum.repos.d/
RUN sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
RUN sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
RUN yum -y install vim

ENV MYPATH /usr/local
WORKDIR $MYPATH

ENV JAVA_HOME /usr/local/jdk1.8.0_311  # 这里的版本取决于你下载的jdk的版本。请认真检查！
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.64
ENV CATALINA_BASH /usr/local/apache-tomcat-9.0.64
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080
CMD /usr/local/apache-tomcat-9.0.64/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.64/bin/logs/catalina.out

# 2.制作镜像
$ docker build -t tomcat-leo .

# 3.查看镜像
$ docker images

# 4.启动
$ docker run -d -p 9090:8080 --name tomcat-leo01 -v /home/leo/build/tomcat/test:/usr/local/apache-tomcat-9.0.64/webapps/test -v /home/leo/build/tomcat/logs:/usr/local/apache-tomcat-9.0.64/logs images_id

# 5.检查挂载的目录以及在test目录下创建WEB-INF文件夹和web.xml文件以及index.jsp文件
$ cd /home/leo/build/tomcat/test
$ vim index.jsp
$ mkdir WEB-INF
$ cd WEB-INF
$ vim web.xml

# 6.访问
http://ip:9090/test

```

index.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Hello, Leo </title>
</head>
<body>
Hello World!
<%
        System.out.println("-----my test log-----");
%>
</body>
</html>
```

web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5" 
xmlns="http://java.sun.com/xml/ns/javaee"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
xsi:schemaLocation="http://java.sun.com/xml/ns/javaee 
http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">

<welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>index.html</welcome-file>
</welcome-file-list>

</web-app>
```

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220717120345164.png" alt="image-20220717120345164" style="zoom:77%;" />

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220717120424520.png" alt="image-20220717120424520" style="zoom:67%;" />
