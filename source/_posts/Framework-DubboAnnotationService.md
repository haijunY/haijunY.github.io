---
title: dubbo的注解方式服务
categories: 技术框架
---
在dubbo.xml中添加配置，指向需要扫描的包
```xml
<dubbo:annotation package="com.xxx.xxx"/>
```
如果是服务端，则在对应的实现类添加com.alibaba.dubbo.config.annotation.Service注解，
如果是消费端，则添加注解com.alibaba.dubbo.config.annotation.Reference