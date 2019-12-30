---
title: dubbo的springboot示例
categories: 技术框架
---
使用springboot整合dubbo，不需要任何xml文件配置，请先安装zookeeper
## 构建过程
项目结构如图
![image.png](http://118.25.96.88/images/Framework-DemoDubboSpringboot-1.png)
![image.png](http://118.25.96.88/images/Framework-DemoDubboSpringboot-2.png)
![image.png](http://118.25.96.88/images/Framework-DemoDubboSpringboot-3.png)

主pom.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<packaging>pom</packaging>
	<modules>
		<module>dubbodemo-springboot-interface</module>
		<module>dubbodemo-springboot-consumer</module>
	</modules>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.1.2.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.haijunyin</groupId>
	<artifactId>dubbodemo-springboot-parent</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>dubbodemo-springboot-parent</name>
	<description>Demo project for Spring Boot</description>

	<properties>
		<java.version>1.8</java.version>
	</properties>

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
## provider
pom.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<artifactId>dubbodemo-springboot-parent</artifactId>
		<groupId>com.haijunyin</groupId>
		<version>0.0.1-SNAPSHOT</version>
	</parent>
	<groupId>com.haijunyin</groupId>
	<artifactId>dubbodemo-springboot-provider</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>dubbodemo-springboot-provider</name>
	<description>Demo project for Spring Boot</description>

	<packaging>war</packaging>
	<properties>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>

		<dependency>
			<groupId>com.haijunyin</groupId>
			<artifactId>dubbodemo-springboot-interface</artifactId>
			<version>0.0.1-SNAPSHOT</version>
		</dependency>

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

		<!-- springbootdubbo -->
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>spring-boot-starter-dubbo</artifactId>
			<version>1.0.0-SNAPSHOT</version>
			<exclusions>
				<exclusion>
					<groupId>org.springframework.boot</groupId>
					<artifactId>spring-boot-starter-web</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
		<dependency>
			<groupId>com.github.sgroschupf</groupId>
			<artifactId>zkclient</artifactId>
			<version>0.1</version>
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

application.properties
```
dubbo.application.name=dubbodemo-springboot-provider
dubbo.application.owner=yhj
dubbo.consumer.check=false
dubbo.consumer.timeout=30000
dubbo.enabled=true
dubbo.monitor.protocol=registry
dubbo.protocol.charset=UTF-8
dubbo.protocol.name=dubbo
dubbo.protocol.payload=104857600
dubbo.protocol.port=28138
dubbo.protocol.threadpool=fixed
dubbo.protocol.threads=500
dubbo.provider.check=false
dubbo.provider.timeout=60000
dubbo.provider.token=true
dubbo.registry.address=localhost:2181
dubbo.registry.check=true
dubbo.registry.group=dubbo
dubbo.registry.protocol=zookeeper
dubbo.registry.subscribe=true
dubbo.registry.timeout=60000
dubbo.scan=com.haijunyin.dubbodemo.springboot.provider
```
DubbodemoSpringbootProviderApplication.java
```java
package com.haijunyin.dubbodemo.springboot.provider;

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
@ComponentScan(value = "com.haijunyin.dubbodemo.springboot.provider")
@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})
@EnableAsync
public class DubbodemoSpringbootProviderApplication extends SpringBootServletInitializer {

	@Override
	protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
		return builder.sources(DubbodemoSpringbootProviderApplication.class);
	}

	public static void main(String[] args) {
		SpringApplication.run(DubbodemoSpringbootProviderApplication.class, args);
	}

}

```
HelloServiceImpl.java
```java
package com.haijunyin.dubbodemo.springboot.provider.services.impl;

import com.alibaba.dubbo.config.annotation.Service;
import com.haijunyin.dubbodemo.springboot.services.HelloService;

@Service
public class HelloServiceImpl implements HelloService {

    @Override
    public String hello(String name) {
        System.out.println("服务端收到服务：...hello...");
        return "你好：" + name;
    }

}
```
## consumer
pom.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>dubbodemo-springboot-parent</artifactId>
        <groupId>com.haijunyin</groupId>
        <version>0.0.1-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>dubbodemo-springboot-consumer</artifactId>

    <packaging>war</packaging>

    <dependencies>
        <dependency>
            <groupId>com.haijunyin</groupId>
            <artifactId>dubbodemo-springboot-interface</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>

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

        <!-- springbootdubbo -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>spring-boot-starter-dubbo</artifactId>
            <version>1.0.0-SNAPSHOT</version>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-web</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>com.github.sgroschupf</groupId>
            <artifactId>zkclient</artifactId>
            <version>0.1</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```
application.properties
```
dubbo.application.name=dubbodemo-springboot-consumer
dubbo.application.owner=yhj
dubbo.consumer.check=false
dubbo.consumer.timeout=30000
dubbo.enabled=true
dubbo.monitor.protocol=registry
dubbo.protocol.charset=UTF-8
dubbo.protocol.name=dubbo
dubbo.protocol.payload=104857600
dubbo.protocol.port=28138
dubbo.protocol.threadpool=fixed
dubbo.protocol.threads=500
dubbo.provider.check=false
dubbo.provider.timeout=60000
dubbo.provider.token=true
dubbo.registry.address=localhost:2181
dubbo.registry.check=true
dubbo.registry.group=dubbo
dubbo.registry.protocol=zookeeper
dubbo.registry.subscribe=true
dubbo.registry.timeout=60000
dubbo.scan=com.haijunyin.dubbodemo.springboot.consumer
```

DubbodemoSpringbootConsumerApplication.java
```java
package com.haijunyin.dubbodemo.springboot.consumer;

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
@ComponentScan(value = "com.haijunyin.dubbodemo.springboot.consumer")
@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})
@EnableAsync
public class DubbodemoSpringbootConsumerApplication extends SpringBootServletInitializer {

	@Override
	protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
		return builder.sources(DubbodemoSpringbootConsumerApplication.class);
	}

	public static void main(String[] args) {
		SpringApplication.run(DubbodemoSpringbootConsumerApplication.class, args);
	}

}

```

HelloController.java
```java
package com.haijunyin.dubbodemo.springboot.consumer.controller;

import com.alibaba.dubbo.config.annotation.Reference;
import com.haijunyin.dubbodemo.springboot.services.HelloService;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@RestController
public class HelloController {

    @Reference
    HelloService helloService;

    @RequestMapping(value = "hello")
    public void hello(@RequestParam(defaultValue = "name") String name, HttpServletResponse resp) throws IOException {
        String message = helloService.hello(name);
        //输出，解决浏览器乱码问题
        resp.setHeader("Content-type", "text/html;charset=UTF-8");
        resp.setCharacterEncoding("UTF-8");
        resp.getWriter().write(message);
    }

}
```

## interface
pom.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>dubbodemo-springboot-parent</artifactId>
        <groupId>com.haijunyin</groupId>
        <version>0.0.1-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>dubbodemo-springboot-interface</artifactId>


</project>
```
HelloService.java
```java
package com.haijunyin.dubbodemo.springboot.services;

public interface HelloService {

    String hello(String name);

}
```

## 运行结果
启动provider和consumer，浏览器中输入
```
首先启动zookeeper,然后配置启动provider的tomcat，然后配置启动consumer的tomcat，在浏览器中输入
```
http://localhost:8072/hello?name=小明
```
返回结果
```
你好：小明
```
说明demo成功
```