---
title: springmvc的xml配置
categories: 技术框架
---
## 构建过程
项目结构如图
![image.png](http://118.25.96.88/images/Framework-DemoSpringmvcXmlConfig-1.png)

pom.xml，添加springmvc相关依赖
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.haijunyin</groupId>
    <artifactId>springmvcdemo-xmlconfig</artifactId>
    <version>1.0-SNAPSHOT</version>

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
    </dependencies>
</project>
```
HelloController.class
```java
package com.haijunyin.springmvcdemo.xmlconfig.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@Controller
@RequestMapping("/hello")
public class HelloController {

    //返回String类型代表页面跳转
    @RequestMapping(value = "/ref",method = RequestMethod.GET)
    public String refHello(){
        System.out.println("HelloController...refHello...");
        return "hello";
    }

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
index.jsp
```html
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
        <load-on-startup>1</load-on-startup>
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
springmvc-servlet.xml,这里的命名与web.xml中spring配置的servletName相对应
```xml
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

    <!-- 启用注解 -->
    <mvc:annotation-driven />

    <!-- 配置视图分发器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          id="internalResourceViewResolver">
        <!-- 前缀 -->
        <property name="prefix" value="/WEB-INF/static/" />
        <!-- 后缀 -->
        <property name="suffix" value=".html" />
    </bean>
</beans>
```
## 运行结果
项目构建完成后启动tomcat
![image.png](http://118.25.96.88/images/Framework-DemoSpringmvcXmlConfig-2.png)

![image.png](http://118.25.96.88/images/Framework-DemoSpringmvcXmlConfig-3.png)

![image.png](http://118.25.96.88/images/Framework-DemoSpringmvcXmlConfig-4.png)

