---
title: 4、SpringmvcThymeleaf示例
date: 2018-08-23 15:07:00
categories: FrameSpringmvc
tags: [Springmvc,Thymeleaf]
---
## 构建过程
项目结构如图
<img src="../images/Framework-DemoSpringmvcThymeleaf-1.png">

pom.xml引入相关jar包

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.haijunyin</groupId>
    <artifactId>springmvcdemo-thymeleaf</artifactId>
    <version>1.0-SNAPSHOT</version>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.6</source>
                    <target>1.6</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
    <packaging>war</packaging>

    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>4.3.8.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>4.3.8.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
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

    </dependencies>

</project>
```
web.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" id="WebApp_ID" version="3.0">
    <display-name>web02</display-name>
    <!-- 首页定义 -->
    <welcome-file-list>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>

    <!-- springmvc的servlet定义 -->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    </servlet>

    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!-- 注册字符集过滤器 -->
    <filter>
        <filter-name>CharacterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

</web-app>
```
springmvc-servlet.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.1.xsd
        http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.1.xsd">

    <!-- 包扫描 -->
    <context:component-scan base-package="com.haijunyin.springmvcdemo"/>

    <!-- 静态资源交给默认servlet处理 -->
    <mvc:default-servlet-handler />
    <!--<mvc:resources location="/static/" mapping="/static/**" />-->
    <!-- 启用注解 -->
    <mvc:annotation-driven />

    <!-- thymeleaf视图分发器 -->
    <bean class="org.thymeleaf.spring4.view.ThymeleafViewResolver">
        <property name="templateEngine" ref="templateEngine"/>
        <property name="characterEncoding" value="UTF-8"/>

        <!-- 该属性控制controller返回的string类型是为结果值还是页面跳转，如果加上此行，则需要return xxx.html格式 -->
        <!--<property name="viewNames" value="*.html,*.xhtml"/>-->

    </bean>

    <!--使用thymeleaf解析 -->
    <bean id="templateResolver"
          class="org.thymeleaf.spring4.templateresolver.SpringResourceTemplateResolver">
        <property name="prefix" value="/WEB-INF/templates/" />
        <property name="suffix" value=".html" />
        <property name="templateMode" value="HTML5" />
        <property name="characterEncoding" value="UTF-8"/>
        <property name="cacheable" value="false" /> <!-- 页面缓存 -->
    </bean>

    <!-- thymeleaf模板引擎 -->
    <bean id="templateEngine" class="org.thymeleaf.spring4.SpringTemplateEngine" >
        <property name="templateResolver" ref="templateResolver"/>
    </bean>

</beans>
```
index.jsp
```xml
<%@ page language="java" contentType="text/html; charset=utf-8"
         pageEncoding="utf-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head></head>
    <body>
        <a href="/hello/ref">hello</a>
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
HelloController.class
```java
package com.haijunyin.springmvcdemo.xmlconfig.controller;

import com.haijunyin.springmvcdemo.xmlconfig.entities.User;
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
User.class
```java
package com.haijunyin.springmvcdemo.xmlconfig.entities;

import javax.xml.bind.annotation.XmlRootElement;

@XmlRootElement // mediatype 转为xml
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
## 运行结果
启动tomcat运行
<img src="../images/Framework-DemoSpringmvcThymeleaf-2.gif">

## 总结
在sprigmvc模式中，控制层controller用于处理业务逻辑并返回结果集，controller不会做视图渲染，而是将视图渲染交给视图解析器处理，springmvc有许多视图解析器，如InternalResourceViewResolver、GroovyMarkupViewResolver、FreeMarkerViewResolver、ThymeleafViewResolver，它们是接口ViewResolver的子类，视图解析器用于渲染对应的模板，比如ThymeleafViewResolver渲染thymeleaf模板，本文是thymeleaf的一个demo