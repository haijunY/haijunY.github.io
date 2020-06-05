---
title: Hystrix
date: 2019-11-03 15:00:00
categories: 微服务
tags: [Hystrix]
---

## 概念

微服务架构中，根据业务拆分成一个个的服务，服务与服务直接可以相互调用，在Spring Cloud可以用RestTemplate+Ribbon和Feign来调用。为了保证其高可用，单个服务通常会集群部署。由于网络原因或者自身原因，服务并不能保证100%可以，如果单个服务出现问题，调用这个服务就会出现线程阻塞，此时若有大量的请求涌入，Servlet容器的线程资源会被消耗完毕，导致服务瘫痪，服务于服务之间的依赖性，故障会传播，会对整个微服务系统造成灾难性的后果，这是服务故障的“雪崩”效应，为了解决这个问题，业界提出了断路器模型

## 准备工作

一个Eureka-server工程，一个服务工程demo-eurekaprovider1

### ribbon中使用断路器

改造demo-ribbon工程，依赖加入Hystrix

```pom
<!-- 断路器  -->
<dependency>
	<groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
    </dependency>
```

启动类加入注解@EnableHystrix

```java
@EnableHystrix //开启断路器
@SpringBootApplication
public class DemoRibbonApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoRibbonApplication.class, args);
    }

}
```

HelloServiceImpl哦加入注解@HystrixCommand，并实现相关错误返回方法

```java
    @HystrixCommand(fallbackMethod = "hiError")
    public String hello(){
        String result = restTemplate.getForObject("http://eureka-provider/hello/hello/", String.class);
        return result;
    }
    public String hiError(){
        return "hiError";
    }

```

启动demo-ribbon服务，此时调用localhost:1235/ribbon，可见服务器正常返回，因为此时demo-provider1是正常启动的，

此时把demo-provider1服务停掉，再次调用localhost:1235/ribbon

服务返回hiError,断路器起作用了

### Feigin中使用断路器

feigin是自带断路器的，所以不用单独加依赖，也不用单独加注解，我们需要写一个服务实现需要断路的服务,加入Spring管理

```java
@Component
public class SchedualServiceHiHystric implements HelloService {
    @Override
    public String hello() {
        return "hiError";
    }
}
```

然后在对应的FeiginClient注解上加入fallback

```java
@FeignClient(value = "eureka-provider", fallback = SchedualServiceHiHystric.class)
public interface HelloService {
    @RequestMapping(value = "/hello/hello",method = RequestMethod.GET)
    String hello();
}
```

同样的请求操作，可见断路器起作用了







