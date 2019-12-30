---
title: servlet的xml配置
categories: 技术框架
---
## 构建过程
项目结构如图
![image.png](http://118.25.96.88/images/Framework-DemoServletXmlConfig-1.png)
pom.xml依赖，导入servlet包

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.haijunyin</groupId>
    <artifactId>servletdemo-xmlconfig</artifactId>
    <version>1.0-SNAPSHOT</version>

    <packaging>war</packaging>

    <dependencies>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
        </dependency>
    </dependencies>

</project>
```
HelloServlet.java继承HttpServlet
```java
package com.haijunyin.servletdemo.xmlconfig.servlet;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class HelloServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("HelloServlet...doGet...");
        resp.getWriter().write("hello...");
    }
}
```
web.xml配置映射
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" id="WebApp_ID" version="3.0">
    <display-name>web02</display-name>
    <welcome-file-list>
        <welcome-file>index.html</welcome-file>
        <welcome-file>index.htm</welcome-file>
        <welcome-file>index.jsp</welcome-file>
        <welcome-file>default.html</welcome-file>
        <welcome-file>default.htm</welcome-file>
        <welcome-file>default.jsp</welcome-file>
    </welcome-file-list>
    <servlet>
        <!-- 声明Servlet对象 -->
        <servlet-name>HelloServlet</servlet-name>
        <!-- 上面一句指定Servlet对象的名称 -->
        <servlet-class>com.haijunyin.servletdemo.xmlconfig.servlet.HelloServlet</servlet-class>
        <!-- 上面一句指定Servlet对象的完整位置，包含包名和类名 -->
    </servlet>
    <servlet-mapping>
        <!-- 映射Servlet -->
        <servlet-name>HelloServlet</servlet-name>
        <!--<servlet-name>与上面<Servlet>标签的<servlet-name>元素相对应，不可以随便起名  -->
        <url-pattern>/hello</url-pattern>
        <!-- 上面一句话用于映射访问URL -->
    </servlet-mapping>
</web-app>
```
index.jsp请求页
```html
<%@ page language="java" contentType="text/html; charset=utf-8"
         pageEncoding="utf-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head></head>
    <body>
        <a href="/hello">hello</a>
    </body>
</html>
```
## 运行结果
把应用加入tomcat启动
![image.png](http://118.25.96.88/images/Framework-DemoServletXmlConfig-2.png)



