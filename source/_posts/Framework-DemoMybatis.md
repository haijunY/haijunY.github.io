---
title: mybatis入门
date: 2018-08-23 15:07:00
categories: 技术框架
---
MyBatis 是一款优秀的持久层框架，它支持定制化 SQL、存储过程以及高级映射，本文通过一个demo把MyBatis进行简单构建
[参考文档](http://www.mybatis.org/mybatis-3/)
[demo地址](https://github.com/haijunY/framedemo_mybatis)
## 构建过程
项目结构如图所示，仅有简单的几个文件
![image.png](http://118.25.96.88/images/Framework-DemoMybatis-1.png)
pom.xml，依赖mybatis的包和mysql驱动

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.company</groupId>
    <artifactId>item</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <!--mybatis依赖包-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.1</version>
        </dependency>
        <!--mysql依赖包-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.39</version>
        </dependency>
    </dependencies>
</project>
```
config.xml，配置数据库连接信息和mapper映射路径
```xml
<?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <environments default="development">
    <environment id="development">
        <transactionManager type="JDBC" />
        <!-- 配置数据库连接信息 -->
        <dataSource type="POOLED">
            <property name="driver" value="com.mysql.jdbc.Driver" />
             <property name="url" value="jdbc:mysql://localhost:3306/db_item" />
             <property name="username" value="root" />
             <property name="password" value="123456" />
         </dataSource>
     </environment>
    </environments>
    <mappers>
        <!-- 注册userInfoMapper.xml文件 -->
        <mapper resource="mapper/UserMapper.xml"/>
    </mappers>
</configuration>
```
UserMapper.xml，定义好命名空间后，简单地写一个查询，其它更加复杂的写法[参考文档](http://www.mybatis.org/mybatis-3/)
```xml
<?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
 <mapper namespace="com.company.mapper.UserMapper">
     <select id="findById" parameterType="java.lang.String"
         resultType="com.company.entities.User">
         select * from user where id=#{id}
     </select>
 </mapper>
```
text.java，加载文件，构建会话，执行语句
```java
package com.company.test;

import com.company.entities.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.Reader;

public class Test {

    public static void main(String[] args) throws IOException {
        //mybatis的配置文件
        String resource = "conf.xml";
        //使用MyBatis提供的Resources类加载mybatis的配置文件（它也加载关联的映射文件）
        Reader reader = Resources.getResourceAsReader(resource);
        //构建sqlSession的工厂
        SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(reader);
        //创建能执行映射文件中sql的sqlSession
        SqlSession session = sessionFactory.openSession();
        /**
         * 映射sql的标识字符串，
         * com.company.mapper.userMapper是userMapper.xml文件中mapper标签的namespace属性的值，
         * getUser是select标签的id属性值，通过select标签的id属性值就可以找到要执行的SQL
         */
        String statement = "com.company.mapper.UserMapper.findById";//映射sql的标识字符串
        //执行查询返回一个唯一user对象的sql
        User user = session.selectOne(statement, "1");
        System.out.println(user);
    }
}

```
## 运行结果
![image.png](http://118.25.96.88/images/Framework-DemoMybatis-2.png)

