---
title: springboot的简单web示例
categories: 技术框架
---
## 构建过程
项目结构如图
![image.png](http://118.25.96.88/images/Framework-DemoSpringbootSimpleWeb-1.png)

pom.xml导入相关依赖包
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.1.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.haijunyin</groupId>
    <artifactId>springbootdemo-simple</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>springbootdemo-simple</name>
    <description>Demo project for Spring Boot</description>

    <packaging>war</packaging>
    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <!-- 排除内置容器，排除内置容器导出成war包可以让外部容器运行spring-boot项目-->
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-tomcat</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.0</version>
            <scope>provided</scope>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```
SpringbootdemoSimpleApplication.class
```java
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;
import org.springframework.boot.builder.SpringApplicationBuilder;
import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.scheduling.annotation.EnableAsync;

/**
 * 继承SpringBootServletInitializer，因为继承SpringBootServletInitializer是继承WebApplicationInitializer的，而servlet容器启动的时候
 * 会将WebApplicationInitializer相关的所有子类实例化(这也是servlet3.0以上的版本提供支持)，所以我们还需要在pom.xml
 * 文件中导入servlet3.0及以上的版本
 */
@ComponentScan(value = "com.haijunyin.springbootdemo")
@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})
@EnableAsync
public class SpringbootdemoSimpleApplication extends SpringBootServletInitializer{

    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
        return builder.sources(SpringbootdemoSimpleApplication.class);
    }

//  public static void main(String[] args) {
//      SpringApplication.run(SpringbootdemoSimpleApplication.class, args);
//  }

}
```
HelloController.class
```java
package com.haijunyin.springbootdemo.simple.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@RestController
@RequestMapping("/hello")
public class HelloController {

    //使用RequestParam注解直接获取值
    @RequestMapping(value = "/show",method = RequestMethod.POST)
    public void showHello(@RequestParam(value = "name")String name, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("ShowHelloServlet...doPost...");
        //获取值，并设置编码
        System.out.println("name=" + name);
        //输出，解决浏览器乱码问题
        resp.setHeader("Content-type", "text/html;charset=UTF-8");
        resp.setCharacterEncoding("UTF-8");
        resp.getWriter().write("我很好，你呢？");
        System.out.println("输出...");
    }

}
```
index.html
```html
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head></head>
<body>
<a href="/hello.html">hello</a>
</body>
</html>
```
hello.html
```html
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta charset="utf-8" />
    </head>
    <body>
        <form action="/hello/show" method="post">
            <input type="text" name="name" value="你好">
            <input type="submit" value="查看">
        </form>
    </body>
</html>
```
application.properties无内容
## 运行结果
启动tomcat
![image.gif](http://118.25.96.88/images/Framework-DemoSpringbootSimpleWeb-2.gif)

