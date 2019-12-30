---
title: 悲观锁和乐观锁
date: 2018-07-28 15:07:00
categories: 高并发
tags: [锁]
---
## 悲观锁
总是假设最坏的情况，每次拿数据的时候都认为别人会修改，所以在拿数据的时候都会先上锁
Java同步关键字synchronized
mysql的select ... for update
oracle支持select ... for update no wait
## 乐观锁
乐观锁认为，在一般的情况下数据不会发生冲突，仅在数据提交更新时，才会做一次校验
通常在表中加一个version版本字段，每次更新数据的时候+1，这样更新的时候会校验，当前版本号是否正确，如果不正确，则报错进行异常处理