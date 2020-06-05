---
title: 3、SpringCloudGateway过滤器
date: 2020-06-03 15:00:00
categories: SpringCloudGateway
tags: [Gateway]

---

predict决定了请求哪一个路由处理，在路由处理之前，需要经过“pre”类型的过滤器，处理返回响应之后，可以由“post”类型的过滤器处理
filter的作用和生命周期
由filter工作流程点，可以知道filter有着非常重要的作用，在“pre”类型的过滤器可以做参数校验、权限校验、流量监控、日志输出、协议转换等，在“post”类型的过滤器可以做响应内容、响应头的修改、日志的输出、流量监控等。
Spring Cloud Gateway同zuul类似，有“pre”和“post”两种方式的filter。客户端请求先经过“pre”类型的filter，然后将请求转发到具体的业务服务，收到业务服务的响应之后，再经过“post”类型的filter处理，最后响应到客户端。
与zuul不同的是，filter除了分为“pre”和“post”两种方式外，在Spring Cloud Gateway中，filter的作用范围还可分为另外两种：一种是针对单个路由的gateway filter，它在配置文件中的写法同predict类似，另外一种是针对于所有
路由的global gateway filter

#### Gateway filter
过滤器允许以某种方式修改传入的HTTP请求或传出的HTTP响应。过滤器可以限定作用在某些特定的请求路径上，Spring Cloud Gateway包含许多内置的GatewayFilter工厂
GatewayFilter工厂和Predicate工厂类似，都是在配置文件application.yml中配置，遵循了约定大于配置的思想，只需要在配置文件配置GatewayFilter Factory的名称，而不需要写全类名，比如AddRequestHeaderGatewayFilterFactory
只需要在配置文件中写AddRequestHeader。
Spring Cloud Gateway内置的过滤器工厂一览表如下：
 ![Spring内置过滤器工厂](../../images/Spring内置过滤器工厂.png)
关于GatewayFilter的用法这里不再举例了，因为前面已经举过了，接下来看看自定义过滤器

##### 自定义过滤器
自定义过滤器需要实现GatewayFilter和Ordered两个接口，我们在gateway-first中写一个RequestTimeFIlter如下
```java
public class RequestTimeFilter implements GatewayFilter, Ordered {
    private static final Log log = LogFactory.getLog(GatewayFilter.class);
    private static final String REQUEST_TIME_BEGIN = "requestTimeBegin";
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        exchange.getAttributes().put(REQUEST_TIME_BEGIN, System.currentTimeMillis());
        return chain.filter(exchange).then(Mono.fromRunnable(() -> {
            Long startTime = exchange.getAttribute(REQUEST_TIME_BEGIN);
            if(startTime != null){
                log.info(exchange.getRequest().getURI().getRawPath() + ": " + (System.currentTimeMillis() - startTime) + "ms");
            }
        }));
    }

    // 过滤器优先级，值越大优先级越低
    @Override
    public int getOrder() {
        return 0;
    }
}
```
 这是一个自定义的过滤器用来打印服务请求处理的时间，那么我们需要把它加入到相应的路由工厂中，如下：
 ```java
    @Bean
    public RouteLocator customerRouteLocator(RouteLocatorBuilder builder){
        return builder.routes()
                .route(p -> p.
                        path("/customer/**")
                        .filters(f -> f.filter(new RequestTimeFilter())
                        .addResponseHeader("X-Response-Default-Foo", "Default-Bar"))
                        .uri("http://httpbin.org:80")
//                        .order(0)
//                        .id("customer_filter_router")
                )
                .build();
    }
 ```
启动服务，访问http://localhost:1111/customer，控制台打印
```text
2020-06-03 10:09:05.866  INFO 25552 --- [ctor-http-nio-1] o.s.cloud.gateway.filter.GatewayFilter   : /customer: 205ms
```
证明自定义过滤器生效
##### 自定义过滤器工厂
过滤器工厂的顶级接口是GatewayFilterFactory，我们可以直接继承它的两个抽象类来开发AbstractGatewayFilterFactory和AbstractNameValueGatewayFilterFactory，这两个抽象类的区别是前者接收一个参数，后者接收两个参数
现在需要自定义一个过滤器工厂，将请求日志打印出来，需要使用一个参数，我们参照RedirectToGatawayFilterFactory写法
```java
public class RequestTimeGatewayFilterFactory extends AbstractGatewayFilterFactory<RequestTimeGatewayFilterFactory.Config> {

    private static final Log log = LogFactory.getLog(GatewayFilter.class);
    private static final String REQUEST_TIME_BEGIN = "requestTimeBegin";
    private static final String KEY = "withParams";

    public List<String> shortcutFieldOrder() {
        return Arrays.asList("withParams");
    }

    public RequestTimeGatewayFilterFactory() {
        super(Config.class);
    }

    @Override
    public GatewayFilter apply(Config config) {
        return (exchange, chain) -> {
            exchange.getAttributes().put(REQUEST_TIME_BEGIN, System.currentTimeMillis());
            return chain.filter(exchange).then(
                    Mono.fromRunnable(() -> {
                        Long startTime = exchange.getAttribute(REQUEST_TIME_BEGIN);
                        if (startTime != null) {
                            StringBuilder sb = new StringBuilder(exchange.getRequest().getURI().getRawPath())
                                    .append(": ")
                                    .append(System.currentTimeMillis() - startTime)
                                    .append("ms");
                            if (config.isWithParams()) {
                                sb.append(" params:").append(exchange.getRequest().getQueryParams());
                            }
                            log.info(sb.toString());
                        }
                    })
            );
        };
    }

    public static class Config{
        private boolean withParams;

        public boolean isWithParams() {
            return withParams;
        }

        public void setWithParams(boolean withParams) {
            this.withParams = withParams;
        }
    }

}
```
然后需要将此自定义的过滤器工厂加入Spring管理，可直接在类上加注解@@Component，也可以定义成一个Bean放入配置类中
```java
    @Bean
    public RequestTimeGatewayFilterFactory elapsedGatewayFilterFactory() {
        return new RequestTimeGatewayFilterFactory();
    }
```
然后加个配置测试下该类
```yml
server:
  port: 1112
spring:
  cloud:
    gateway:
      routes:
        - id: elapse_route
          uri: http://httpbin.org:80/get
          filters:
            - RequestTime=false
          predicates:
            - After=2017-01-20T17:42:47.789-07:00[America/Denver]
```
浏览器访问地址，本地会打印出请求的时间，证明配置成功
#### Global Filter
Spring Cloud Gateway根据作用范围划分为GatewayFilter和GlobalFilter，二者区别如下：
    GatewayFilter:需要通过spring.cloud.routes.filters配置在具体的路由下，只作用在当前路由上或通过spring.cloud.default-filter配置在全局，作用在所有路由上
    GlobalFilter:全局过滤器，不需要在文件中配置，作用在所有的路由上，最终通过GatewayFilterAdapter包装成GatewayFilterChain可识别的过滤器，它为请求业务及路由的URI转换为真实业务服务的请求地址的核心过滤器，系统初始化时加载，并作用在每个路由上
Spring Cloud Gateway框架内置的GlobalFilter如下
![全局过滤器](../../images/SpringCloudGateway全局过滤器.png)
图中每一个GlobalFilter都作用在每一个router上，能够满足大多数需求，但是如果遇到业务上的定制，可能需要自己编写GlobalFilter。

##### 自定义全局过滤器
编写自己的GlobalFilter，该GlobalFilter会校验请求中是否包含了请求参数“token”，如果不包含，则不转发路由
```java
@Component
public class TokenFilter implements GlobalFilter, Ordered {
    Logger logger= LoggerFactory.getLogger( TokenFilter.class );
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        String token = exchange.getRequest().getQueryParams().getFirst("token");
        if(token == null || token.isEmpty()){
            logger.info("token is empty...");
            exchange.getResponse().setStatusCode(HttpStatus.UNAUTHORIZED);
            return exchange.getResponse().setComplete();
        }
        return chain.filter(exchange);
    }

    @Override
    public int getOrder() {
        return -100;
    }
}
```
启动工程，访问，控制台打印
```text
token is empty...
```
自定义全局过滤器生效

源码：https://github.com/haijunY/demos-past/tree/master/spring-cloud-gateway/


