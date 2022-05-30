# Gitee

---

## Gitee简介

**Gitee**（码云）是开源中国于2013年推出的基于[Git](https://zh.wikipedia.org/wiki/Git)的代码托管和协作开发平台，提供中国本土化的[代码托管](https://zh.wikipedia.org/wiki/分散式版本控制)服务。

## Gitee作用

- 代码托管
- Gitee+hexo做个人博客

## Gitee使用

> 前提条件：
>
> ​	这里默认你已经安装了Git，并且已经注册好了Gitee账号。

### 代码托管篇

#### 使用SSH方式与Gitee通讯

- 创建SSH公钥

  ```
  # 在你的cmd命令窗口生成公钥，格式如下：
  ssh-keygen -t ed25519 -C "xxxxx@xxxxx.com"
  # 例如，我的
  ssh-keygen -t ed25519 -C "971465407@qq.com"
  # 连按三次回车，生成成功后到C:\Users\97146\.ssh路径下复制id_ed25519.pub文件内容到Gitee的公钥中。
  ```

- 推送代码到Gitee

  ```
  # 1.首先你要在Gitee上新建一个仓库，并复制它的ssh路径：
  # 	git@gitee.com:liuwenxiu/webpack-study.git
  # 2.将你准备好的代码推送上去
  # 	2.1 如果你的代码中没有.git文件，请先执行
  #		git init
  #	2.2 与远程仓库建立联系.
  #		git remote add origin git@gitee.com:liuwenxiu/webpack-study.git
  #	2.3 如果之前已经建立了联系，但是你想要换一个仓库，请执行下面命令，不换则跳过此步。
  #		git remote set-url origin git@gitee.com:用户个性地址/仓库名称.git
  #		git remote set-url origin git@gitee.com:liuwenxiu/webpack-study.git
  #	2.4 如果远程分支已经存在了一些文件，比如readme.md，gitignore等，需要先拉取远程分支并merge
  #		git pull origin master 
  #		git merge master --allow-unrelated-histories
  #	2.5 最后推送到远程分支
  #		git add .
  #		git commit -m "提交message"
  #		git push --set-upstream origin master
  #		或简写：git push -u origin master
  #		
  #  此时，你的本地项目就推送到Gitee远程仓库啦！
  #  注：
  #	建议Gitee上创建一个空的仓库，没有readme等任何文件。
  #	这样我们只需要执行2.1->2.2->2.5三步即可。
  #  	取消与远程分支的关联：
  #		git remote remove origin 
  ```

#### 使用Http的方式与Gitee通讯

- 推送代码到Gitee

  ```
  # 1.首先你要在Gitee上新建一个仓库，并复制它的http路径：
  # 	https://gitee.com/liuwenxiu/webpack-study.git
  # 2.将你准备好的代码推送上去
  # 	2.1 如果你的代码中没有.git文件，请先执行
  #		git init
  #	2.2 然后更改远程仓库地址，执行 
  #		git remote add origin https://gitee.com/用户个性地址/仓库名称.git
  #		git remote add origin https://gitee.com/liuwenxiu/webpack-study.git
  #  	2.3 此时idea会弹出让你输入用户名/密码的窗口，输入正确的用户名密码。
  # 此时，你的本地项目就推送到Gitee远程仓库啦！
  ```

#### 关于如何选择License

<img src="D:/liuwx/it%20study/%E5%90%8E%E5%8F%B0/java%E8%BF%9B%E9%98%B6/git/Gitee.assets/image-20220529155729569.png" alt="image-20220529155729569" style="zoom:40%;" />

#### 小结

> ​	以上两种方式均可以实现代码托管到Gitee。
>
> ​	但是，实际情况中使用SSH方式更多。

### 基于Gitee+Hexo搭建个人博客

#### 环境准备

- Node.js

  > 如果没有Node.js，详情可参考Vue中Node.js安装环节。

- Git

  > 如果没有Git，去官网下载安装即可。

#### Hexo

1. 简介

  > Hexo 是一个基于 node.js 的快速、简洁且高效的博客框架 
  >
  > Hexo 使用 [Markdown](http://daringfireball.net/projects/markdown/)（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。
  >
  > **他是个静态网页，所以你每次修改文件后都需要重新部署，比较蛋疼。**
  >
  > 官网：https://hexo.io/zh-cn/

2. 安装

  ```bash
  npm install -g hexo-cli
  ```

3. 验证

  ```
  C:\Users\97146>hexo version
  hexo-cli: 4.3.0
    
  // 常用命令网站
  https://hexo.io/zh-cn/docs/commands.html
  ```

4. 本地运行

  ```
  hexo init <folder>		# 初始化
  cd <folder>				# 进入
  npm install				# 安装必要
  hexo generate			# 生成静态页面
  hexo server        		# 运行本地服务 http://localhost:4000/
  ```

  ```
  .
  ├── .deploy       #需要部署的文件
  ├── node_modules  #Hexo插件
  ├── public        #生成的静态网页文件
  ├── scaffolds     #模板
  ├── source        #博客正文和其他源文件, 404 favicon CNAME 等都应该放在这里
  |   ├── _drafts   #草稿
  |   └── _posts    #文章
  ├── themes        #主题
  ├── _config.yml   #全局配置文件
  └── package.json
  ```

5. 更换主题

  > 进入官网找到主题，选择自己喜欢的进行下载

  1. 切换到Hexo的themes目录下

    ```bash
    git clone https://github.com/klugjo/hexo-theme-magnetic.git
    ```

  2. 更改_config.yml文件中的theme属性值

    ```
    ...
    
    # Extensions
    ## Plugins: https://hexo.io/plugins/
    ## Themes: https://hexo.io/themes/
    theme: hexo-theme-magnetic
    
    ...
    ```

  3. 重启Hexo

    ```bash
    hexo clean  //清除一下缓存
    hexo g  	//生成静态页面
    hexo s 		//开启本地服务器
    ```

6. 发表文章

  - 方式一：创建md文件

    ```
    $ hexo new "hexo study"
    INFO  Validating config
    INFO  Created: D:\liuwx\it study\Hexo\source\_posts\hexo-study.md
    ```

  - 方式二：将写好的.md文件放入Hexo项目的`source` 目录下的`_posts` 文件夹中

    ```
    hexo clean  //清除一下缓存
    hexo g  	//生成静态页面
    hexo s 		//开启本地服务器
    访问md文件名称:
    	http://localhost:4000/MarkDown学习笔记
    ```


#### 搭建博客

##### 上传Gitee

1. 创建一个空仓库

   ```
   git@gitee.com:liuwenxiu/myblog.git
   ```

2. 修改配置文件`_config.yml`

   ```
   deploy:
     type: git         							# type为git
     repo: git@gitee.com:liuwenxiu/myblog.git  # 仓库的URL
   ```

3. 安装hexo插件

   ```
   cnpm install hexo-deployer-git --save
   ```

4. 上传

   ```
   hexo deploy
   hexo d		// 简写
   ```

5. 上传成功！

   注：如果是https模式需要设置用户名/密码

   ```
   git config --global user.name "xx" # 设置用户名（git的注册昵称）
   git config --global user.email "xx@126.com" # 设置gitee邮箱（gitee的注册邮箱）
   ```

##### GiteePages服务

> 网页解析服务
>
> 一个支持Jekyll、Hugo、Hexo静态网站的服务

- 启动GiteePages服务



- 访问

  ```
  http://liuwenxiu.gitee.io/blog
  ```

#### 搭建个人云图床+PicGo管理

##### 什么是图床

> 图床是一个存放图片并提供访问链接的地方。
>
> 我们可以使用Gitee创建个人仓库来存放图片，然后通过Gitee服务器提供访问链接就可以实现自己的个人图床了。
>
> 不过gitee有时需要面临审核（国家政策原因），我们的图床成了私有就不能访问了，建议使用github当作图床服务器。

##### 什么是PicGo

> PicGo是一个用于快速上传图片并获取图片URL链接的工具。
>
> 支持插件扩展功能，甚至可以直接搭配Typora编辑器进行图片复制上传。

##### 下载并安装

```
https://github.com/Molunerfinn/PicGo/releases
```

##### Gitee版

###### 配置Gitee图床

> picGo主页面 -> 插件设置 -> gitee-uploader 1.1.2

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529153720990.png" alt="image-20220529153720990" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529153752497.png" alt="image-20220529153752497" style="zoom:50%;" />

```
repo：填写你的图床仓库名称
branch：分支名
path：图片存储路径
token：填写上面教程中你设置的私人令牌
message：不需要填
```

###### 创建Gitee图片仓库

> 创建一个仓库，公开的就行。
>
> 当然现在(2022/5/29)肯定是不行的了。gitee不允许图床public

##### GitHub版

###### 创建图片仓库

> 创建一个公开的仓库即可。

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529154031591.png" alt="image-20220529154031591" style="zoom:50%;" />

###### 创建token

> settings -> Developer settings -> Personal access tokens
>
> 按照以上步骤并结合下面的图片生成token，
>
> 然后需要记住你的token，已经放到flomo里了。

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529154312725.png" alt="image-20220529154312725" style="zoom:50%;" />

###### PicGo配置

> picGo里设置github图床。
>
> 设置完成后在上传区上传张图片验证下是否ok了。

![image-20220529160247458](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529160247458.png)

### Gitee同步GitHub上的代码

> 把github上的仓库同步到gitee上，并且在代码更改后能够同步提交代码到gitee和github上

> gitee官网提供：
>
> https://gitee.com/help/articles/4284#article-header0
