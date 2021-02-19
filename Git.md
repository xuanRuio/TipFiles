# Git

---

### 提交操作

- 工作区

  👇git add .

-  暂存区

  👇git commit -m "message"    **工作区——>本地仓库：git commit -am 'message' **

- 本地仓库

  👇git push

- 远程仓库

---

### 历史版本查看

- ~~git log~~

  信息较多；不记录回退或删除版本；

- git reflog 

  显示简洁；能够记录回退或删除版本；

---

### 撤销文件修改

> **仅能撤销工作区和暂存区的代码，仓库区的需要回退版本**

- 工作区撤销（未执行add操作）

  git checkout

- 暂存区撤销（add操作之后）

  git reset HEAD 文件名 （到达工作区）-->git checkout (工作区撤销)

---

### 回退版本

- git reset --hard 版本号
- git reset --hard HEAD^   (一个开始符^代表上一个版本)

---

### 对比版本

- 工作区版本对比

  git diff 版本号 文件名   （git diff HEAD test.py）

- 仓库区版本对比

  git diff 版本号 版本号

---

### 删除文件

- git rm 文件名

---

### 远程仓库

- HTTPS

  git clone "github//..."

  - 设置记住密码

    ~~git config --global credential.helper cache(默认十五分钟)~~

    ~~git config --global credential.helper 'cache -- timeout=3600'（一小时)~~

    git config --global credential.helper store (永久)

- SSH

  - 生成密钥

    ssh-keygen -t rsa -C "xxx.com(注册邮箱)" 
    
    cd /c/Users/xuanRui/.ssh/

---

 ### 团队协同开发

- 拉取远程仓库

  git pull

- 提交至远程仓库

  git commit -am "message"

  git push
  
  git remote add [origin] [repository url] (**第一次push前需操作**)

---

### 标签

- 本地标签 

  git tag -a 'V1.0' -m '功能描述'

- 推送标签到远程仓库

  git push origin v1.0

---

### 分支操作

- 查看所有分支

  git branch 或 git branch -a

- 创建新分支&切换到该 分支

  git branch 分支名&git checkout 分支名

- 远程推送分支

  **git push -u origin 分支名**

- 新建新分支并切到该分支

  git  checkout -b 分支名 

- 合并分支

  切换到主分支 -> 合并分支 -> 推送到远程

  git checkout master -> git merage ->git push

---

### 远程仓库操作

- 查看远程仓库信息

  git remote -v

- 显示某个远程仓库的信息

  git remote show [remote url]

- 添加远程版本库(**第一次push前需操作**)

  git remote add [origin] [repository url]

- 删除远程仓库

  git remote rm name


> **origin就是一个名字，它是在你clone一个托管在Github上代码库时，git为你默认创建的指向这个远程代码库的标签，origin指向的是repository，master只是这个repository中默认创建的第一个branch。当你git push的时候因为origin和master都是默认创建的，所以可以这样省略，但是这个是bad practice，因为当你换一个branch再git push的时候，有时候就纠结了**

---

### git忽略文件配置

- 详见：https://www.cnblogs.com/Yiutto/articles/4678578.html