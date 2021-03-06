---
title: 3、Spring事务
date: 2019-11-03 15:00:00
categories: FrameSpring
tags: [Spring,事务]

---

### Spring事务

ACID原则：原子性、一致性、隔离性、持久性

并发下产生的问题：

脏读：A事务未提交数据，B事务却读到了

不可重复读：A事务读数据，B事务修改数据未提交，A事务再次读到的数据变了，不可重复读强调数据的更新

幻读：A事务读取一片数据，B事务新增一条数据，A事务再次读取时多了一条数据，幻读强调数据新增删除

隔离级别：

| 隔离级别                     | 脏读   | 不可重复读 | 幻读   |
| ---------------------------- | ------ | ---------- | ------ |
| 读未提交（Read-Uncommitted） | 允许   | 允许       | 允许   |
| 不可重复读（Read-Committed） | 不允许 | 允许       | 允许   |
| 可重复读（Repeatable-Read）  | 不允许 | 不允许     | 允许   |
| 串行化（Serializable）       | 不允许 | 不允许     | 不允许 |

SpringJDBC中的传播行为：

| 事务传播行为类型          | 说明                                                         |
| ------------------------- | ------------------------------------------------------------ |
| PROPAGATION_REQUIRED      | 如果当前没有事务，就新建一个事务，如果已经存在一个事务，加入到这个事务 |
| PROPAGATION_SUPPORTS      | 支持当前事务，如果没有当前事务，就以非事务方式执行           |
| PROPAGATION_MANDATORY     | 使用当前的事务，如果当前没有事务，就抛出异常。               |
| PROPAGATION_REQUIRES_NEW  | 新建事务，如果当前存在事务，把当前事务挂起。                 |
| PROPAGATION_NOT_SUPPORTED | 以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。   |
| PROPAGATION_NEVER         | 以非事务方式执行，如果当前存在事务，则抛出异常。             |
| PROPAGATION_NESTED        | 如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行与PROPAGATION_REQUIRED类似的操作。 |

源码：https://github.com/haijunY/demos-past/tree/master/frame-spring