---
title: 1、Spring
date: 2019-11-03 15:00:00
categories: FrameSpring
tags: [Spring]

---

### OOP、BOP、IOC、DI、AOP

OOP：面向对象编程，把业务需求转化成代码的过程

BOP：面向Bean的编程，管理Bean，由此Spring的IOC就是干这个事情

IOC：控制反转，控制的是JavaBean，反转给谁，把Bean的实例化交给BeanFactory管理，IOC实现具体分为三大步：定位、加载、注册，定位是定位相关配置文件的位置，有的是用xml文件定义的，有的是用注解定义的，把它们用BeanDefinitionReader做统一定义，用BeanDefinitionReader加载所有的类到List<BeanDefinition>，然后把这个集合放到一个map对象里面Map<String, BeanDefinition> beanDefinitionMap，这是一个伪IOC容器，然后把Bean实例化，取决于bean是否需要延迟加载lazyInit=true表示延迟加载，否则是容器启动的时候就加载，singleton=true表示单例，只能有一个对象，否则可以定义相应的beanName来实例化多个

DI：依赖注入，用反射将需要注入的属性赋值的过程，DI主要分为两步：实例化、依赖注入，调用getBean方法会实例化，或者有的Bean是在容器启动的时候实例化，依赖注入这块主要是针对属性而言的，通过反射依次注入相关属性，如果当时没有对应的属性实例，则记录下来等待下一次轮询，这样可以解决循环依赖的问题

AOP：既然BOP是面向Bean的编程，那么AOP就是面向切面的编程，通过规则（包路径+表达式）定义需要面向的范围，然后定义前置、后置、异常等处理，过程分三步：创建代理对象、调用具体方法、触发通知，AOP是生成一个新的代理类，当然肯定是在初始化Bean的时候开始的，也就是initializeBean()方法，根据类特征创建相应的代理对象，默认用JDK代理，如果是接口的（JDK代理搞不定的）用Cglib代理，调用具体方法的时候，调用的是Proxy的invoke()方法，最终触发对应的每一个通知的invoke()方法



### Spring中核心的模块有哪些

spring-core：	核心

spring-beans：beans管理，如BeanFactory、FactoryBean

spring-context：各种容器，ApplicationContext

spring-aop：切面模块

spring-jdbc：链接数据库，如JdbcTemplate

spring-orm：关系映射模块，jpa和hibernate5

spring-webmvc：MVC框架，DispatcherServlet

spring-test：测试模块

spring-expression：异常模块

### BeanFactory和FactoryBean

BeanFactory是一个工厂类，IOC容器，所有被管理的Bean从这里获得，比如ApplicationContext，而FactoryBean看命名就知道是一种Bean，实现了这个接口的Bean不是一个普通的Bean，它也可以被BeanFactory管理，但是获得的对象并不是Bean本身，而是一个泛型Object，实例化这个Object是通过重写getObject()方法做到的，如果需要返回容器本身，则需要在BeanFactory的getBean(name)参数中加入前缀"&"符号。比如ProxyFactoryBean、JndiObjectFactoryBean

### Spring有哪些设计模式，举例说明

工厂模式：BeanFactory

单例和原型：创建Bean的时候

模板+钩子方法：DispatcherServlet的initStrategies()方法，它初始化了SpringMVC的9大组件

委派模式：以Strategy结尾

策略模式：以Resolver结尾，都是策略模式的体现，比如SpringMVC的view视图组件，异常拦截组件

代理：Spring中有两种代理，JDK代理和Cglib代理，在AOP中会根据实际情况选用哪一种

适配器：HandlerAdapter



### SpringMVC的启动和调用过程

启动过程：启动时容器会通过HttpServlet的init()方法调用，由于DispatcherServlet重写了init()方法，所以会被调用，而这个方法中会调用onRefresh()方法，然后会调用initStrategies()方法，初始化9大组件，其中重要的组件有：HandMapping，HandAdapter，视图组件等，HandMapping用于存储Method(反射拿到)、映射URL(注解取到)、对应实例(IOC容器拿到)，HandAdapter是真正的处理类，通过反射的机制去调用HandMapping中的Method方法，参数可以从request和@RequestParam注解中拿到，视图组件处理返回

调用过程：收到请求后，会通过HttpServlet的service()方法调用到DispatcherServlet的service()方法，然后调用到doDispatcher()方法，首先会通过url找到对应的HandMapping，然后找到HandAdapter处理后返回ModelAndView对象，然后通过对应的视图组件将ModelAndView渲染成对应的类型，json或html，返回到前端显示

源码：https://github.com/haijunY/demos-past/tree/master/frame-spring