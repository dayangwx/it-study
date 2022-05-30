# Zookeeper

---

## 1、Zookeeper概述

### 1-1.定义

​	开源的分布式**协调**服务。

- 理解

​	负责存储和管理大家都关心的数据，

​	并且接受观察者的注册，
​	一旦这些数据的状态发生变化，
​	zk就会通知已经注册了的观察者做出反应

- 总结

  **存储了大家都关心的数据并且被观察，当这些数据发生变化时，告诉观察者。**

### 1-2.特点

集群环境下：

- 一个server，多个folower
- **只要有半数以上节点正常工作，就可以正常提供服务**
- 全局一致性：所有节点的数据相同
- 数据更新原子性，一次数据更新，要么全成功，要么全失败
- 数据实时性，一定时间内，数据是最新的。

### 1-3.数据结构



### 1-4.应用场景

- 统一命名服务

- 统一配置管理

- 统一集群管理

- 服务器节点动态上下线

- 软负载均衡

  ​	计数，然后将请求分发给少的服务器

## 2、Zookeeper安装部署

### 2-1.单机

#### 2-1-1.下载并解压

​	官网：https://zookeeper.apache.org/releases.html

​	解压：

```bash
tar -zxvf apache-zookeeper-3.5.6-bin.tar.gz -C /hadoop/
mv apache-zookeeper-3.5.6-bin/ zookeeper-3.5.6
```

#### 2-1-2.修改配置

- 1）：将{zk path}/conf下的zoo_sample.cfg 修改为 zoo.cfg【先备份】

  ```bash
  cp zoo_sample.cfg zoo.cfg
  tar -zxvf apache-zookeeper-3.5.6-bin.tar.gz -C /hadoop/
  mv apache-zookeeper-3.5.6-bin/ zookeeper-3.5.6
  ```

- 2)：打开 zoo.cfg 文件，修改 dataDir 路径

  > ​	用来保存zk数据，相当于是zk树节点的根节点

  ```bash
  dataDir=/zookeeper/data_1
  ```

- 3）： 在 /zookeeper 这个目录上创建 data_1 文件夹

  ```
  mkdir /zookeeper/data_1
  ```

#### 2-1-3.修改环境变量

- 1）：打开配置文件

  ```
  vim /etc/profile
  ```

- 2）：在配置文件中添加以下内容

  ```bash
  #ZOOKEEPER
  export ZOOKEEPER_HOME=/opt/apache-zookeeper-3.6.2-bin
  export PATH=$PATH:$ZOOKEEPER_HOME/bin
  ```

- 3）：使文件生效

  ```bash
  source /etc/profile
  ```

- 4）：验证

  ```bash
  echo $ZOOKEEPER_HOME
  ```

#### 2-1-4.操作命令

- 1）：启动

  > 指定配置文件，如不指定，默认加载conf/zoo.cfg

  ```
  zkServer.sh start /opt/apache-zookeeper-3.6.2-bin/conf/zoo1.cfg
  ```

- 2）：查看状态

  > 指定配置文件，如不指定，默认加载conf/zoo.cfg

  ```bash
  zkServer.sh status /opt/apache-zookeeper-3.6.2-bin/conf/zoo1.cfg
  ```

- 3）：连接客户端

  > 可以连接任意可以访问的ip:port的zk服务，当然本机2181就可以省略

  ```bash
  zkCli.sh -server 127.0.0.1:2181
  ```

- 4）：退出客户端

  ```bash
  quit
  ```

- 5）：停止

  > 指定配置文件，如不指定，默认加载conf/zoo.cfg

  ```
  zkServer.sh stop /opt/apache-zookeeper-3.6.2-bin/conf/zoo1.cfg
  ```

- 6）：使用查询占用端口停止进程

  ```shell
  [root@tech1 bin]# lsof -i:2181
  COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
  java    28391 root   62u  IPv6 640767      0t0  TCP *:eforward (LISTEN)
  
  [root@tech1 bin]# kill -9 PID
  使用这个命令就可以杀死占用2181端口的进程。
  ```

  







### 2-2.集群

#### 2-2-1.集群规划

​	

#### 2-2-2.解压安装重命名



#### 2-2-3.配置服务器



#### 2-2-4.配置zoo.cfg文件



#### 2-2-5.修改环境变量



## 3、Zookeeper内部原理

### 3-1.选举机制

#### 3-1-1.半数机制

> ​	集群中半数以上机器存活，集群可用。所以 Zookeeper 适合安装==奇数台==服务器

> ​	Zookeeper 虽然在配置文件中并没有指定 Master 和 Slave。
>
> ​	但是，Zookeeper工作时，是有一个节点为 Leader，其他则为 Follower，
> ​	**Leader 是通过内部的选举机制临时产生的**

#### 3-1-2.选举过程

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/%E9%80%89%E4%B8%BE%E8%BF%87%E7%A8%8B.png" style="zoom:45%;" />

### 3-2.节点类型

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/zk%E8%8A%82%E7%82%B9%E7%B1%BB%E5%9E%8B.png" style="zoom:40%;" />

- 持久节点

  ​	**客户端和服务器端断开连接后，创建的节点不删除**

  ​	① 持久化目录节点

  ​		客户端与 Zookeeper 断开连接后，该节点依旧存在

  ​	② 持久化顺序编号目录节点

  ​		客户端与 Zookeeper 断开连接后，该节点依旧存在，只是 Zookeeper 给该节点名称进行顺序编号

- 暂时节点

  ​	**客户端和服务器端断开连接后，创建的节点自己删除**

  ​	① 临时目录节点

  ​		客户端与 Zookeeper 断开连接后，该节点被删除

  ​	② 临时顺序编号目录节点

  ​		客户端与 Zookeeper 断开连接后，该节点被删除，只是 Zookeeper 给该节点名称进行顺序编号。

  注：

​	1.创建 znode 时设置顺序标识，znode 名称后会附加一个值，
​	顺序号是一个单调递增的计数器，由父节点维护。

​    2.在分布式系统中，顺序号可以被用于为所有事件进行全局排序，
​    这样客户端可以通过顺序号推断事件的顺序



