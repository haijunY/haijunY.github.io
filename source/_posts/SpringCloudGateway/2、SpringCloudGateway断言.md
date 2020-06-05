---
title: 2、SpringCloudGateway断言
date: 2020-06-03 15:00:00
categories: SpringCloudGateway
tags: [Gateway]

---

介绍几种断言
#### Header Route Predicate Factory
这次我们直接使用配置的方式实现，新建一个工程gateway-two，引入gateway的包，加入配置
```yml
spring:
  cloud:
    gateway:
      routes:
        - id: s1
          uri: http://httpbin.org:80/get
          predicates:
            - Header=X-Request-Id, \d+
```
该配置表示，当请求的Header中有X-Request-Id的header名，且header值为数值时，请求会被路由到配置的uri，可以使用idea自带的REST Client工具给请求头添加值，可以测试效果
#### Cookie Route Predicate Factory
Cookie Route Predicate Factory需要两个名字参数，一个cookie的名字，一个值，可以为正则表达式，它用于匹配请求中，带有该名称的cookie和cookie能正则匹配的请求
我们新建一个配置文件application-s2.yml，修改application.yml中的active属性为s2，使s2文件生效，启动服务，然后在请求头中加入参数Cookie:name=forezp可看到效果
```yml
spring:
  cloud:
    gateway:
      routes:
        - id: s2
          uri: http://httpbin.org:80/get
          predicates:
            - Cookie=name, forezp
```
#### Host Route Predicate Factory
Host Route Predicate Factory需要一个参数即hostname，它可以使用.*等去匹配host。这个参数会匹配请求头中的host值，一致则请求正确转发,见配置application-host_route.yml
关于其它断言就不再一一列举了，用到的时候看官方文档

源码：https://github.com/haijunY/demos-past/tree/master/spring-cloud-gateway/gateway-two


