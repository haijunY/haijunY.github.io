---
title: 7、Springboot原理
date: 2018-08-23 15:07:00
categories: FrameSpring
tags: [Spring,Springboot]
---
承接springmvc的工作原理，这里探讨springboot是如何工作的，由springboot的启动类SpringbootdemoSimpleApplication.java开始，我们知道，如果想把springboot的项目打包放到tomcat里运行，需要将启动类继承SpringBootServletInitializer
```java
public class SpringbootdemoSimpleApplication extends SpringBootServletInitializer{
```
因为SpringBootServletInitializer是继承WebApplicationInitializer的
```java
public abstract class SpringBootServletInitializer implements WebApplicationInitializer {
```
在springmvc中，WebApplicationInitializer 就是启动类，原理是通过servlet的特性进行加载的，所以我们需要导入servlet的依赖包，并排除springboot内置的tomcat包
```pom
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
```
注解@SpringBootApplication是springboot启动的关键注解，该注解下有@EnableAutoConfiguration注解，开启自动配置，会把相关的配置类加载到spring容器中，而这些配置类的全类名，就在文件spring.factories下
<img src="../images/Framework-SpringbootPrinciple-1.png">
比如WebMvcAutoConfiguration就是springmvc的配置类，加载此配置类，就相当于普通的springmvc项目的springmvc-servlet.xml配置。

## 总结
本文以springmvc为引入点，说明一个普通的springboot项目整合过程，关于springboot的更加详细的原理剖析参考文章: [springboot之启动原理解析及源码阅读](https://www.cnblogs.com/shamo89/p/8184960.html)
