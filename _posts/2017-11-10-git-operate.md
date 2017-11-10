---
layout: post
#标题配置 
title:  Git操作
#时间配置
date:   2017-11-10 16:34:00 +0800
#大类配置
categories: document
#小类配置
tag: 教程
---

* content
{:toc}


# 1. 同步本地仓库到远程仓库
1. 创建本地文件, github上创建好对应的仓库
2. git init  (在该目录下) //建立git仓库
3. git add README.md
4. git commit –m “提交信息”
5. git remote add origin  https://github.com/LymanXu/gitTest.git
6. git push –u origin master

# 2. 提取远程仓库
1. git fetch origin 从远程仓库下载新的分支和数据
2. git merge origin/master  进行更新master分支，这两步相当于 git pull

# 3. 删除远程仓库
1. git remote –v  查看远程仓库
2. git remote add origin2  https://github.com/LymanXu/gitTest.git  添加远程仓库

# 4. 推送到远程仓库
1. git push [alias] [branch]
将你的[branch]分支推送成为[alias]远程仓库上的[branch]分支

# 5. 本地创建与合并分支
1. git checkout –b dev1
2. 此时新建dev1分支，并切换到该分支，进行文件的修改，add，commit
3. git checkout master  dev1的工作完成后，切回master分支
4. git merge dev1 把dev1上的工作成果合并到master上
5. git branch –d dev1  合并完成后，删除分支dev1

# 6. pull的使用
1. git pull origin dev1:master   取回origin上的dev1分支与本地的master分支合并
2. git pull origin dev1  取回远程分支origin/dev1 和当前分支合并
命令2等价于  
git fetch origin
git merger origin/dev1
4. git会自动在本地分支和远程分支之间建立追踪关系，git clone的时候，本地分支和远程分支同名的建立追踪关系
如果当前分支和远程分支存在追踪关系，git pull 可以省略远程分支名
git pull orign
这个命令表示，本地当前分支和对应的origin上的追踪分支进行合并

# 7. 从GitHub新建分支同步到本地
1. github新建branch
2. 在本地目前有master分支，git pull origin,同步origin中的数据改动
3. git checkout dev1  本地切换到dev1分支
4. 本地分支进行修改，提交到origin对应的分支，git add，git commit，git push origin dev1

# 8. 从本地新建分支同步到GitHub
1. 在本地新建一个分支 git branch dev1
2. git checkout dev1  切到新的分支
3. 在该分支进行工作
4. git push origin dev1 将新的分支发布到GitHub上
5. git branch –d dev1 在本地删除分支
6. 远程端删除分支的命令 git push origin :dev1 加冒号

# 9. 将开发分支dev1合并到master，更新GitHub
1. git checkout master ,git pull origin [master]  同步master的最新内容
2. tortoiseGit RevisionGraph 进行代码review
3. git merge dev1  将dev1分支的内容合并到master
4. 出现冲突，解决冲突
5. git push orgin master 提交合并的代码

# 10. 项目的fork与之后的同步
1. 从原项目fork到自己GitHub，生成一个与原项目互不影响的项目副本
2. 从自己的GitHub将项目clone到本地，在本地进行修改
3. 添加原作者项目的remote地址，将代码fetch过来
git remote add soureorigin  http://XXX源项目地址
git fetch sourceorigin  将原项目的数据拉取到本地
查看本地项目目录 git remote –v
4. 合并代码，将原项目的更新代码合并到本地
git checkout master
git merge sourceorigin/master
如果出现冲突
5. 解决冲突，push代码
git commit –am ”更新原项目的代码到主分支”
git push origin
git push –u origin master

