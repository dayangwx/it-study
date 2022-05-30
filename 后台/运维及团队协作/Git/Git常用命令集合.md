# Git常用命令集合

---

1. 如何取消与远程仓库的关联

   ```
   git remote remove origin
   
   git remote -v #没有任何内容展示说明已经取消成功
   ```

2. 本地工程如何与远程==空仓库==建立连接并推送代码

   空仓库：没有readme等文件。

   ```
   git init
   
   git remote add origin https://gitee.com/liuwenxiu/hellovue.git
   
   git push -u origin master 
   ```

   

