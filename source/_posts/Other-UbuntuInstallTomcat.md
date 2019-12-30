---
title: ubuntu安装tomcat
categories: 其它
---
ubuntu是一个开源的Linux操作系统，apt插件用于安装各种应用，它把应用都分好了目录的，我们只需要用命令就可以一键安装各种应用,很多常用的Linux命令之前需要加上sudo以获得权限，比如需要编辑文件应这样写,编辑sudo vi xxx.txt，上传文件sudo rz

## 更新apt插件
```
sudo apt-get update
```
## 安装jdk
```
sudo apt-get update
sudo apt-get install openjdk-8-jdk
```

## 安装tomcat
```
sudo apt-get install tomcat8
```
## 上传War包
```
sudo apt install lrzsz 安装上传下载插件
cd /var/lib/tomcat8/webapps 应用目录
sudo rz  上传War包，这里要加上sudo以获得权限
```
## 启动tomcat
```
sudo /etc/init.d/tomcat8 restart  启动tomcat8
```