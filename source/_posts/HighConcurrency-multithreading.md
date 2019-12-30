---
title: 多线程
date: 2018-07-28 15:07:00
categories: 高并发
tags: [线程]
---
## 线程基本介绍和创建方式
参考[Java 多线程编程](http://www.runoob.com/java/java-multithreading.html)
## jdk自带的线程池
java.util.concurrent包提供了线程相关的类，ThreadPoolExecutor用于创建一个线程池，通常我们会这样创建一个线程池
```java
private ThreadPoolExecutor scheduleExecutor =
            new ThreadPoolExecutor(100, 100, 0, TimeUnit.MILLISECONDS, new ArrayBlockingQueue<>(100),
                    new NamedThreadFactory("basic-data"), new LoggerDiscardPolicy());
```
## spring的线程池
spring的线程池是对ThreadPoolExecutor的封装
```java
@Configuration
public class ThreadPoolConfig {
    @Bean("threadPoolTaskExecutor")
    public ThreadPoolTaskExecutor threadPoolTaskExecutor(){
        ThreadPoolTaskExecutor threadPoolTaskExecutor=new ThreadPoolTaskExecutor();
        threadPoolTaskExecutor.setCorePoolSize(4);
        threadPoolTaskExecutor.setKeepAliveSeconds(10);
        threadPoolTaskExecutor.setMaxPoolSize(10);
        threadPoolTaskExecutor.setQueueCapacity(10);
        return threadPoolTaskExecutor;

    }
}
```