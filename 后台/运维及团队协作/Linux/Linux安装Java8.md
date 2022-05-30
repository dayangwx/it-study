# Linux安装Java8

---

### 卸载

- `查看已安装的jdk`

  ```bash
  [root@tech2 ~]# rpm -qa|grep jdk
  java-1.8.0-openjdk-devel-1.8.0.292.b10-1.el7_9.x86_64
  copy-jdk-configs-3.3-10.el7_5.noarch
  java-1.8.0-openjdk-1.8.0.292.b10-1.el7_9.x86_64
  java-1.8.0-openjdk-headless-1.8.0.292.b10-1.el7_9.x86_64/
  ```

- `卸载命令`

  ```bash
  [root@bogon ~]# yum -y remove java-1.8.0-openjdk-devel-1.8.0.292.b10-1.el7_9.x86_64
  [root@tech2 ~]# yum -y remove java-1.8.0-openjdk-1.8.0.292.b10-1.el7_9.x86_64
  [root@tech2 ~]# yum -y remove copy-jdk-configs-3.3-10.el7_5.noarch
  ```

  > `可以多执行上述命令几次，确保所有已安装jdk被卸载`

- `卸载完成之后Java命令不被识别`

  ```bash
  [root@tech2 ~]# java -version
  -bash: java: 未找到命令
  ```

### 下载

- Oracle Jdk

  1. `oracle官网`<a href="http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html" target="_blank">下载</a>`jdk1.8`

  2. `需要登陆时`：

     > `可以百度一个`<a href="https://blog.csdn.net/yuan5025/article/details/108689201" target="_blank">Oracle账户</a>`，或者自己注册。`
     >
     > ```
     > pigide3064@fxseller.com/Azerty1!
     > ```

  3. `下载与Linux系统版本一致的jdk进行安装即可。`

     ![image-20211210155620364](https://note.youdao.com/yws/public/resource/77ebc9a8b6295c7507d70ec085674ca9/xmlnote/WEBRESOURCE71791cbf37bc086ac67006d07544f92c/515)

- Open Jdk

  ​	`在/usr/local/java下执行：`

  ```bash
  $ wget https://mirrors.tuna.tsinghua.edu.cn/AdoptOpenJDK/8/jdk/x64/linux/OpenJDK8U-jdk_x64_linux_hotspot_8u312b07.tar.gz
  ```


### 安装

- `解压到安装目录并修改名称`

  ```bash
  [root@bogon software]# tar -zxvf jdk-8u311-linux-x64.tar.gz -C /usr/local/java/
  [root@tech1 software]# mv jdk1.8.0_33 jdk1.8.0
  ```

- `安装完毕之后在/etc/profile文件末尾添加`

  ```bash
  [root@tech3 java]# vim /etc/profile
  export JAVA_HOME=/usr/local/java/jdk1.8.0
  export JRE_HOME=${JAVA_HOME}/jre
  export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
  export PATH=${JAVA_HOME}/bin:$PATH
  ```

- `使/etc/profile生效`

  ```bash
  [root@tech1 java]# source /etc/profile
  ```

### 验证

- `验证是否安装成功`

  ```bash
  [root@tech1 java]# java -version
  java version "1.8.0_311"
  Java(TM) SE Runtime Environment (build 1.8.0_311-b11)
  Java HotSpot(TM) 64-Bit Server VM (build 25.311-b11, mixed mode)
  ```

  



​	







