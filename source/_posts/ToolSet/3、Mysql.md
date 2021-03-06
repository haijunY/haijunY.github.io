---
title: 3、Mysql
date: 2019-09-03 15:00:00
categories: ToolSet
tags: [Mysql]

---

## 事务

### 数据库事务特性

原子性：要么全部成功，要么全部回滚

一致性：事务的执行不能破坏数据库数据的完整性和一致性，比如在做多表操作时，多个表要么都是事务后新的值，要么都是事务前的旧值

隔离性：多个用户并发访问数据库时，数据库为每个用户执行的事务，不能被其它事务干扰

持久性：一个事务一旦提交成功，对数据库中数据的改变是永久性的

### 事务并发问题

脏读：一个事务处理过程中读取了另一个事务未提交的数据

不可重复读：同一个事务中两次同样的读取返回了不同的值，原因是在过程中有其他事务提交了数据

幻读：一个事务中两次同样的读取返回了不同的值，与不可重复读的区别在于，不可重复读针对的是同一条数据，幻读记录的是增加或删除

### 事务隔离级别

读未提交：A事务还未提交，B在读的时候会把A事务未提交的数据读取，可能发生脏读、不可重复读、幻读

读已提交：A事务还未提交，B在读的时候会读数据库原来的数据，但是如果A事务提交了，B再次读取的时候会读			取新数据，这就导致了B前后两次读取结果不一致（不可重复读），可能发生不可重复读、幻读

可重复读：Mysql默认事务，A事务还未提交，B在读的时候会读数据库原来的数据，如果A事务提交了，B再次读			取的时候不会读取新数据，保证B前后两次读取结果一致（解决不可重复读），可能发生幻读

串行化：最高隔离级别，所有的事务操作都是依次执行，并发读大大下降，性能最差，但是是最安全的

### Mysql性能优化

1、当只要一行数据时使用limit 1查询时如果已知会得到一条数据，这种情况下加上limit 1会提高性能。因为Mysql数据库引擎会在找到一条结果停止搜索，而不是继续查询下一条是否符合标准直到所有记录查询完毕

2、选择正确的数据库引擎Mysql中有两个引擎MyISAM和InnoDB，每个引擎有利有弊。MyISAM适用于一些大量的查询的应用，但对于有大量写功能的应用不是很好。甚至你只需要update一个字段整个表都会锁起来。而别的进程就算是读操作也不行需要等到当前update操作完成之后才能执行。另外，MyISAM对于select count(*)这类操作是超级快的。InnoDB的趋势会是一个非常复杂的存储引擎，对于一些小的应用会比MyISAM还慢，但是支持“行锁”，所以在写操作比较多的时候会比较优秀，并且，它支持很多高级应用，例如：事务

3、用not exists代替not in，not exists用到了连接能够发挥已经建立好的索引的作用，not in不能使用索引

### 引擎

#### InnoDB

InnoDB支持以下几种索引

B+树索引

全文索引

哈希索引

##### B+树索引

B+树是一种平衡查找树，B+树特征：有n个子树的中间节点包含n个元素，每个元素不保留数据，只用来索引，所有数据都保留在叶子节点；所有叶子节点包含元素信息以及指向记录的指针，且叶子节点按关键字自小到大顺序连接；所有的中间节点元素都同时存在于子节点，在子节点元素中是最大（或最小）元素

优势：

单一节点存储更多元素，减少IO

所有查询都要找到叶子节点，查询稳定

所有叶子节点形成有序链表，方便范围查询

一颗B+树可以存放多少条数据？两千万

计算方式：

InnoDB引擎的最小存储单元-页，一个页的大小是16K，假设B+树的高度为2，即存在一个根节点和若干个子节点，那么这颗B+树存放的总记录数为：根节点指针数*单个叶子节点记录行数

假设一条数据的大小为1K，指针大小是6字节，如果存储的是bigint占用8字节，那么一个页中能存放多少个这样的单元呢：即16K/14*16=1170

一颗树存储的数据为1170*(16K/1K)=18720

同样的原理我们可以计算出一个高度为3的B+树可以存放1170*1170*16=21902400 条这样的记录。

##### 全文索引

##### 辅助索引

#### MyISAM






