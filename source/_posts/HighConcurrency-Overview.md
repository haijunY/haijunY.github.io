---
title: 高并发概览
date: 2018-07-28 15:07:00
categories: 高并发
tags: [线程]
---
java的高并发处理主要包括两个方面：安全性和快速响应
## 安全性
业务逻辑安全：[幂等校验](https://www.cnblogs.com/geyifan/p/6128425.html)、[snychronized](https://www.haijunyin.com/HighConcurrency-snychronized.html)、[单例模式和懒加载](https://www.cnblogs.com/jingpeipei/p/5771716.html)；
数据安全：[事务](https://www.haijunyin.com/HighConcurrency-ProgrammaticAndDeclarativeTransactions.html)、[悲观锁和乐观锁](https://www.haijunyin.com/HighConcurrency-PessimisticLockAndOptimisticLock.html)
## 快速响应
架构层：负载均衡、rides、zookeeper集群、静态资源分离、页面缓存、反向代理ngnix
业务层：[多线程](https://www.haijunyin.com/HighConcurrency-multithreading.html)、业务拆分（分离活跃数据）、[异步处理](https://www.haijunyin.com/HighConcurrency-snychronized.html)
数据层：分库分表、索引、缓存、读写分离、水平切分和垂直切分