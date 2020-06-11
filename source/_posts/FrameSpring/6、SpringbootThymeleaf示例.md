---
title: 6、SpringbootThymeleaf示例
date: 2018-08-23 15:07:00
categories: FrameSpring
tags: [Springboot,Thymeleaf]
---
## 构建过程
用springboot构建显然比用springmvc构建方式简洁多了，项目结构如图
<img src="../images/Framework-DemoSpringbootThymeleaf-1.png">

pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.1.2.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.haijunyin</groupId>
	<artifactId>springbootdemo-thymeleaf</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>springbootdemo-thymeleaf</name>
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

		<dependency>
			<groupId>org.thymeleaf</groupId>
			<artifactId>thymeleaf</artifactId>
			<version>3.0.11.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.thymeleaf</groupId>
			<artifactId>thymeleaf-spring4</artifactId>
			<version>3.0.11.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
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
application.properties
```
#thymelea模板配置
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
spring.thymeleaf.mode=HTML5
spring.thymeleaf.encoding=UTF-8
#热部署文件，页面不产生缓存，及时更新
spring.thymeleaf.cache=false
spring.resources.chain.strategy.content.enabled=true
spring.resources.chain.strategy.content.paths=/**
```
HelloController.java
```java
package com.haijunyin.springbootdemo.thymeleaf.controller;

import com.haijunyin.springbootdemo.thymeleaf.entities.User;
import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import java.util.ArrayList;
import java.util.List;

@Controller
@RequestMapping("/hello")
public class HelloController {

    //返回String类型代表页面跳转
    @RequestMapping(value = "/ref",method = RequestMethod.GET)
    public String refHello(){
        System.out.println("HelloController...refHello...");
        return "hello";
    }

    @RequestMapping(value = "/show",method = RequestMethod.POST)
    public String show(ModelMap modelMap) {
        System.out.println("HelloController...show...");
        List<User> users = new ArrayList<User>();
        users.add(new User(3,"张三",23));
        users.add(new User(4,"李四",24));
        modelMap.addAttribute("userList", users);
        modelMap.addAttribute("title", "列表页");
        return "show";
    }

    @RequestMapping(value = "/detail/{id}",method = RequestMethod.GET)
    public String detail(@PathVariable long id, ModelMap modelMap) {
        System.out.println("HelloController...detail...");
        System.out.println("id=" + id);
        if(id == 3){
            modelMap.addAttribute("user", new User(3,"张三",23));
        }else if(id == 4){
            modelMap.addAttribute("user", new User(3,"李四",24));
        }
        modelMap.addAttribute("title", "明细页");
        return "detail";
    }

}
```
User.java
```java
package com.haijunyin.springbootdemo.thymeleaf.entities;

public class User {
    private long id; // 用户的唯一标识
    private String name;
    private int age;

    public User() {
    }

    public User(long id, String name, int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }
    public long getId() {
        return id;
    }

    public void setId(long id) {
        this.id = id;
    }
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```
启动类SpringbootdemoThymeleafApplication.java
```java
package com.haijunyin.springbootdemo.thymeleaf;

import org.springframework.boot.SpringApplication;
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
public class SpringbootdemoThymeleafApplication extends SpringBootServletInitializer {

	@Override
	protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
		return builder.sources(SpringbootdemoThymeleafApplication.class);
	}

//	public static void main(String[] args) {
//		SpringApplication.run(SpringbootdemoThymeleafApplication.class, args);
//	}

}

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
detail.html
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org" >
<head>
    <meta charset="UTF-8">
    <title>今晚天气好</title>
</head>
<body>
<div>
</div>
    <a th:text="${title}"></a>
    <ul>
        <li th:text="${user.id}"></li>
        <li th:text="${user.name}"></li>
        <li th:text="${user.age}"></li>
    </ul>
</body>
</html>
```
show.html
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org" >
<head>
    <meta charset="UTF-8">
    <title>今晚天气好</title>
</head>
<body>
<div>
</div>
    <a th:text="${title}"></a>
    <table border="1">
        <thead>
        <tr>
            <td>ID</td>
            <td>Age</td>
            <td>Name</td>
        </tr>
        </thead>
        <tbody>
        <tr th:if="${userList.size()} eq 0">
            <td colspan="3">没有用户信息！！</td>
        </tr>
        <tr th:each="user : ${userList}">
            <td th:text="${user.id}">1</td>
            <td th:text="${user.age}">11</td>
            <td>
                <a href="view.html" th:href="@{'/hello/detail/' + ${user.id}}"
                   th:text="${user.name}">wss</a></td>
        </tr>
        </tbody>

    </table>
</body>
</html>
```
## 运行结果

<img src="../images/Framework-DemoSpringbootThymeleaf-2.gif">

## 总结
在用springmvc+thymeleaf组合时候，由于配置比较多，而且资料杂，差不多一天才写成demo，然而用springboot+thymeleaf组合，仅仅不到半小时，就把demo调试通了，这得益于springboot的易用，从demo中可以看到，所有的配置项都在application.properties文件里，仅仅从搭建项目来看，不需要了解MVC模式和原理，而使用springmvc则不行，必须知道哪个类是视图解析器，以及哪个类是模板引擎，并给相应参数赋值