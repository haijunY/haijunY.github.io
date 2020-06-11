---
title: 12、docker
date: 2020-06-09 11:27:00
categories: ToolSet
tags: [docker]sudo /etc/init.d/tomcat8 restart  启动tomcat8
---
详细教程见http://www.itmuch.com/docker/00-docker-lession-index/

## 常用命令

```

docker search java //从Docker Hub中搜索含有"java"关键字的镜像
docker rmi hello-world //删除本地hello-world镜像
docker images //查看本地镜像
docker ps //查看容器

```





## docker安装nacos



拉镜像

```
docker pull nacos/nacos-server:1.0.0
```



启动

```
docker run --env MODE=standalone --name nacos -d -p 8848:8848 nacos/nacos-server:1.0.0
```

