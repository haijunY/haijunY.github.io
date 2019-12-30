---
title: List、Set、Map
categories: 面试
---
### List、Set、Map的区别？
List：
1.可以允许重复的对象
          2.可以插入多个null元素
          3.是一个有序容器
          4.实现类有ArrayList、LinkedList和Vector。ArrayList有索引，索引更加用于频繁查询，            LinkedList是链表结构，所以适用于频繁添加和删除
Set：
1.不允许重复对象
2.无序容器
3.只允许一个null
4.流行的实现类有HashSet、LinkedHashSet和TreeSet。最流行的是基于HashMap实现的HashSet
Map：
1.Map是一个键值对的的Entry数组，key是唯一的，value可重复、可为null
2.流行的实现类有HashMap、LinkedHashMap、Hashtable、TreeMap、ConcurrentHashMap，
HashMap线程不安全性能高，Hashtable线程安全性能低，ConcurrentHashMap线程安全性能高

### 什么情况下使用List、Set、Map？
实际上是问他们的优缺点
如果需要有序的话用List，如果经常对元素容器访问用ArrayList，如果经常添加和删除元素用LinkedList，如果需要元素唯一用Set，如果需要以键值对形式存储，用Map，其中HashMap线程不安全性能高，Hashtable线程安全性能低，ConcurrentHashMap线程安全性能高，有并发时用ConcurrentHashMap

### HashSet是如何保证不重复的？
1.首先需要知道hashcode()方法和equals()方法
java中存在一种hash表结构，它通过一种算法，计算出的结果是hash码值；这个算法叫hash算法
hash算法是怎么计算的呢，是通过对象中的成员来计算，如果成员变量是基本数据类型，那么用这个值直接参与计算，如果是引用类型，那么获取这个成员变量的hash值后再参与计算
2.是否存入新元素，首先判断hash码，如果hash码不同，说明是新元素，存入，如果hash码相同，再判断equals，不相等则是新元素，存入，相等不存入

### HashMap线程安全吗？为什么？
不安全
1.数据丢失
2.resize死循环
3.fail-fast

### HashMap的扩容过程？
初始容量（默认16）和加载因子（默认0.75）
当元素个数超过容量*加载因子的时候，进行扩容
1.重新建一个size*2的数组
2.将原数组元素转移到新数组

### 反射？
Java 反射机制是在运行状态中，对于任意一个类，都能够获得这个类的所有属性和方法，对于任意一个对象都能够调用它的任意一个属性和方法。这种在运行时动态的获取信息以及动态调用对象的方法的功能称为Java 的反射机制。