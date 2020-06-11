---
title: 6、git使用
date: 2018-06-12 15:07:00
categories: ToolSet
tags: [git]
---
[中文官网](https://git-scm.com/)
[安装教程](https://jingyan.baidu.com/article/9f7e7ec0b17cac6f2815548d.html)

## 常用
``` bash
git branch 列出本地所有分支
git pull	同步到本地
git pull origin 分支名 	同步远程到本地
git checkout 分支名   切换分支
git add 提交当前目录到暂存区
git commit 提交到本地
git push origin 分支名  提交到远端分支
git reset 返回到上次提交状态
git fetch 同步到本地
```
## 拉分支
``` bash
git clone 项目地址 从远程克隆到本地一个仓库，第一次拉代码
git fetch 远程如果建立了分支，同步到本地然后用git branch -a 可以看到远程分支名
git checkout -b 分支名  创建+切换分支
git pull origin 分支名  从远程拉代码到本地分支
git branch -d 分支名   删除本地分支
```
## 提交代码
按此命令顺序执行，代码会从本地work提交到远程仓库
``` bash
git status
git add .
git commit -m '备注'
git pull
git push 分支名
git branch --set-upstream-to origin/分支名
git push origin HEAD:分支名
```
## 新项目上传步骤
1.在命令行中，输入“git init”，使当前文件夹加入git管理；
2.输入“git add .”（不要漏了“.”），将当前文件夹全部内容添加到git；
3.输入“git commit -m "注释"；
4.输入“git remote add origin https://github.com/user/xxx.git”（git remote add origin 你自己的https地址），连接你的github仓库；
5.输入“git push -u origin master”，上传项目到Github。这里会要求输入Github的账号密码，按要求输入就可以。

## 多账号配置
1.安装git
2.在任意地方右键打开Git Bash Here控制窗口输入：
git config --global user.name "名称"
git config --global user.email "邮箱"
3.ssh-keygen -t rsa -C "邮箱" 回车
再连续3次回车后系统会提示Your public key has been saved in /c/Users/xxx/.ssh/id_rsa.pub
4.找到该路径下的两个文件:id_rsa、id_rsa.pub
5.用编辑器打开id_rsa.pub复制全部内容，将这些内容作为秘钥添加到git账号中
6.测试秘钥是否成功，在Git Bash Here窗口中输入ssh git@https://github.com/user/xxx.git



参考
https://www.jianshu.com/p/83c74c7280fd