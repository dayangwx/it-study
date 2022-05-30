# Nginx

---

## 1、Nginx产生原因

> 一个公司的项目刚刚上线的时候，并发量小，用户使用的少，所以在低并发的情况下，一个jar包启动应用就够了，然后内部tomcat返回内容给用户。

![image-20220529161411266](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529161411266.png)

> 慢慢的，使用平台的用户越来越多了，并发量慢慢增大了，这时候一台服务器满足不了我们的需求了

![image-20220529161430194](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529161430194.png)

>  于是我们横向扩展，又增加了服务器。这个时候几个项目启动在不同的服务器上，用户要访问，就需要增加一个代理服务器了，通过代理服务器来帮我们转发和处理请求。

![image-20220529161450136](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529161450136.png)

> 我们希望这个**代理服务器可以帮助我们接收用户的请求，然后将用户的请求按照规则帮我们转发到不同的服务器节点之上。这个过程用户是无感知的**，用户并不知道是哪个服务器返回的结果，我们还希望他可以**按照服务器的性能提供不同的权重选择**。保证最佳体验！所以我们使用了Nginx。
>
> 作用：
>
>   1：反向代理
>
>   2：负载均衡

## 2、Nginx简介

- Nginx (engine x) 是一个**高性能的HTTP和反向代理web服务器，同时也提供了IMAP/POP3/SMTP服务**。Nginx是由伊戈尔·赛索耶夫为俄罗斯访问量第二的Rambler.ru站点（俄文：Рамблер）开发的，第一个公开版本0.1.0发布于2004年10月4日。2011年6月1日，nginx 1.0.4发布。
- 其特点是**占用内存少，并发能力强**，事实上nginx的并发能力在同类型的网页服务器中表现较好，中国大陆使用nginx网站用户有：百度、京东、新浪、网易、腾讯、淘宝等。在全球活跃的网站中有12.18%的使用比率，大约为2220万个网站。
- Nginx 是一个安装非常的简单、配置文件非常简洁（还能够支持perl语法）、Bug非常少的服务。Nginx 启动特别容易，并且几乎**可以做到7*24不间断运行，即使运行数个月也不需要重新启动**。你还能够不间断服务的情况下进行软件版本的升级。
- Nginx代码完全用C语言从头写成。官方数据测试表明能够**支持高达 50,000 个并发连接数的响应**。

## 3、Nginx的作用

### 3-1.反向代理

> Http代理，反向代理：作为web服务器最常用的功能之一，尤其是反向代理

- 正向代理

  > 代理的是客户端。比如说翻墙的VPN，或者说访问公司内网的VPN

  ![image-20220529161516334](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529161516334.png)

- 反向代理

  > 代理的是服务器的。
  >
  > 简单理解就是说：我nginx代表的是你要访问的服务器，你不用访问服务器了，你访问我就行。
  >
  > 所以用户是无感知的，比如，百度的服务器是在不同的服务器上，我们只需要搜索baidu.com就行



![image-20220529161534186](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529161534186.png)

### 3-2.负载均衡

> Nginx提供的负载均衡策略有2种：内置策略和扩展策略。内置策略为轮询，加权轮询，Ip hash。扩展策略，就天马行空，只有你想不到的没有他做不到的。

- 轮询

![image-20220529161553454](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529161553454.png)

- 加权轮询

![image-20220529161638443](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529161638443.png)

- iphash

  > 对客户端请求的ip进行hash操作，然后根据hash结果将同一个客户端ip的请求分发给同一台服务器进行处理，可以解决session不共享的问题。

​	![image-20220529161657922](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529161657922.png)

### 3-3.动静分离

> 在我们的软件开发中，有些请求是需要后台处理的，有些请求是不需要经过后台处理的（如：css、html、jpg、js等等文件），这些不需要经过后台处理的文件称为静态文件。让动态网站里的动态网页根据一定规则把不变的资源和经常变的资源区分开来，动静资源做好了拆分以后，我们就可以根据静态资源的特点将其做缓存操作。提高资源响应的速度。

![image-20220529161723261](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529161723261.png)

## 4、Nginx安装

> 去ng官网下载需要的版本。

### 4-1.Windows环境

- 下载并解压

- 启动

  > - 直接双击nginx.exe，双击后一个黑色的弹窗一闪而过
  >
  > - 打开cmd命令窗口，切换到nginx解压目录下，输入命令 `nginx.exe` ，回车即可

- 访问

  > 启动成功后访问
  >
  >  http://localhost

- 常用命令

  > nginx -s stop
  >
  > nginx -s reload

- 配置文件

  > nginx.conf

### 4-2.Linux环境

- 下载并解压

  >  1、在 /usr/local/ 下创建 nginx ⽂件夹并进⼊ 
  >
  >  2、将 Nginx 安装包解压到 /usr/local/nginx 中即可。tar -zxvf nginx-1.17.10.tar.gz
  >
  >  解压完之后， /usr/local/nginx ⽬录中会出现⼀个 nginx-1.17.10 的⽬录

- 安装

  > 安装依赖
  >
  > ```shell
  > yum -y install pcre-devel
  > yum -y install openssl openssl-devel
  > ```
  >
  > 进入ng目录下
  >
  > ```shell
  > cd nginx-1.17.10
  > ./configure
  > make && make install
  > ```
  >
  > 安装完成后，Ng可执行文件位于：
  >
  > ```shell
  > /usr/local/nginx/sbin/nginx
  > ```

- 启动

  ```shell
  [root@localhost sbin]# /usr/local/nginx/sbin/nginx
  ```

- 访问

  > http://localhost

  如果访问不了，请检查防火墙：

  ```shell
  # 开启
  service firewalld start
  # 重启
  service firewalld restart
  # 关闭
  service firewalld stop
  # 查看防火墙规则
  firewall-cmd --list-all
  # 查询端口是否开放
  firewall-cmd --query-port=8080/tcp
  # 开放80端口
  firewall-cmd --permanent --add-port=80/tcp
  # 移除端口
  firewall-cmd --permanent --remove-port=8080/tcp
  #重启防火墙(修改配置后要重启防火墙)
  firewall-cmd --reload
  # 参数解释
  1、firwall-cmd：是Linux提供的操作firewall的一个工具；
  2、--permanent：表示设置为持久；
  3、--add-port：标识添加的端口；
  ```

- 常用命令

  ```shell
  停止：
  	/usr/local/nginx/sbin/nginx -s stop
  重新加载配置文件：
  	/usr/local/nginx/sbin/nginx -s reload
  查看是否安装了ng：
  	whereis nginx
  查看ng启动情况：
  	ps aux | grep nginx
  ```

- 配置文件

  > /usr/local/nginx/conf/nginx.conf

  ```shell
  http {
      ...
  	upstream cvzhanshi{    // 代理8081和8082两个服务。server里proxy_pass：http://cvzhanshi
  		server 127.0.0.1:8082/ weight=1; // weight代表权重，表示5次请求，1次走8082
  		server 127.0.0.1:8081/ weight=4; // weight代表权重，表示5次请求，4次走8081
  	}
  
  
      server {
          listen       80;
          server_name  localhost;
  
          location / {
              root   html;
              index  index.html index.htm;
  			proxy_pass http://cvzhanshi;	// 代理8081，
          }
          ...
  }
  ```

  

  
