---
title: 1、SpringClouldGateway入门
date: 2020-06-03 15:00:00
categories: SpringClouldGateway
tags: [Gateway]

---

Spring Cloud Gateway是Spring Cloud官方推出的第二代网关框架，取代zuul网关。网关主要作用是路由转发、权限校验、限流控制。

### 入门
#### 一个简单的DEMO
创建一个项目gateway-first，添加Spring Cloud Gateway配置
```pom
       <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-gateway</artifactId>
        </dependency>
```
注意不用加starer-web配置，因为Spring Cloud gateway已经包含了start-web
写一个Route路由
```java
@Configuration
public class FirstRoute {

    @Bean
    public RouteLocator firstRoutes(RouteLocatorBuilder builder){
        return builder.routes()
                .route(p -> p
                        .path("/get")
                        .filters(f -> f.addRequestHeader("Hello", "World"))
                        .uri("http://httpbin.org:80"))
                .build();
    }

}
```
路由的含义是"/get"路径下的请求都会添加上请求头，并且转发到地址httpbin.org:80，
启动项目，浏览器打开http://localhost:1111/get，浏览器返回
```json
{
  "args": {}, 
  "headers": {
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9", 
    "Accept-Encoding": "gzip, deflate, br", 
    "Accept-Language": "zh-CN,zh;q=0.9", 
    "Content-Length": "0", 
    "Cookie": "last-serviceName=service-hi; token=uuc_token:UUC_TOKEN_1d5b43335-19d9-4acc-980d-36e066110f25", 
    "Forwarded": "proto=http;host=\"localhost:1111\";for=\"0:0:0:0:0:0:0:1:64521\"", 
    "Hello": "World", 
    "Host": "httpbin.org", 
    "Sec-Fetch-Dest": "document", 
    "Sec-Fetch-Mode": "navigate", 
    "Sec-Fetch-Site": "none", 
    "Sec-Fetch-User": "?1", 
    "Upgrade-Insecure-Requests": "1", 
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/83.0.4103.61 Safari/537.36", 
    "X-Amzn-Trace-Id": "Root=1-5ed60871-983f3ee03474c5c87c223d4c", 
    "X-Forwarded-Host": "localhost:1111"
  }, 
  "origin": "0:0:0:0:0:0:0:1, 116.227.75.54", 
  "url": "http://localhost:1111/get"
}
```
可见能正常访问目标地址，并且在请求头中添加了Hello:World
#### 使用Hystrix
路由当中可以直接使用Hystrix，首先引入jar包
```pom
        <!-- 断路器  -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
        </dependency>
```
路由中添加如下代码
```java

                .route(p -> p.
                        path("/hystrix")
                        .filters(f -> f
                                .hystrix(config -> config
                                        .setName("mycmd")
                                        .setFallbackUri("forward:/fallback")))  //访问失败后请求/fallback路径
                        .uri("http://httpbin.org:8000"))    //一个不可访问的地址
                .build();
```
再写一个fallback的请求
```java
    @RequestMapping("/fallback")
    public Mono<String> fallback(){
        return Mono.just("fallback");
    }
```
浏览器访问http://localhost:1111/hystrix，由于路由的是一个不可访问的地址，则会触发断路器去调用fallback请求，结果返回
```text
fallback
```
### 工作流程

请看官网的一张图

![工作流程图](D:/works/haijunY.github.io/source/images/SpringCloudGateway工作流程图.png)

客户端向Spring Cloud Gateway发出请求。如果Gateway Handler Mapping确定请求与路由匹配（这个时候就用到了predicate）,则将其发送到Gateway web Handler处理。Gateway web Hanlder处理请求时会经过一系列的过滤器链。
过滤器链被虚线划分的原因是过滤器链可以在发送代理请求之前或之后执行。先执行所有“pre”过滤器，然后进行代理请求。在发出代理请求之后，收到代理服务器的响应之后执行“post”过滤器。这跟zuul的处理过程很类似。
在执行所有“pre”过滤器时，往往进行了鉴权、限流、日志输出等功能，以及请求头的更改、协议的转换；转发之后收到响应之后，会执行所有的“post”过滤器，在这里可以响应数据进行修改，比如响应头、协议的转换等。
在上面的处理过程中，请求和路由进行匹配会用到predicate，它决定了一个请求走哪个路由

源码：https://github.com/haijunY/demos-past/tree/master/spring-cloud-gateway/gateway-first