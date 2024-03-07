# Spring Cloud Zuul 网关的介绍及使用

> 原文：[`c.biancheng.net/view/5380.html`](http://c.biancheng.net/view/5380.html)

Zuul 是 Netflix OSS 中的一员，是一个基于 JVM 路由和服务端的负载均衡器。提供路由、监控、弹性、安全等方面的服务框架。Zuul 能够与 Eureka、Ribbon、Hystrix 等组件配合使用。

Zuul 的核心是过滤器，通过这些过滤器我们可以扩展出很多功能，比如：

#### 1）动态路由

动态地将客户端的请求路由到后端不同的服务，做一些逻辑处理，比如聚合多个服务的数据返回。

#### 2）请求监控

可以对整个系统的请求进行监控，记录详细的请求响应日志，可以实时统计出当前系统的访问量以及监控状态。

#### 3）认证鉴权

对每一个访问的请求做认证，拒绝非法请求，保护好后端的服务。

#### 4）压力测试

压力测试是一项很重要的工作，像一些电商公司需要模拟更多真实的用户并发量来保证重大活动时系统的稳定。通过 Zuul 可以动态地将请求转发到后端服务的集群中，还可以识别测试流量和真实流量，从而做一些特殊处理。

#### 5）灰度发布

灰度发布可以保证整体系统的稳定，在初始灰度的时候就可以发现、调整问题，以保证其影响度。

## 使用 Zuul 构建微服务网关

#### 创建一个 Maven 项目 zuul-demo，在 pom.xml 中增加 Spring Cloud 项目的依赖，然后加入 Zuul 的依赖，代码如下所示。

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
</dependency>

属性文件中增加配置信息：

spring.application.name=zuul-demo
server.port=2103

zuul.routes.biancheng.path=/biancheng/**
zuul.routes.biancheng.url=http://c.biancheng.net

启动类的代码如下所示：

```

@EnableZuulProxy
@SpringBootApplication
public class App {
    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}
```

通过 zuul.routes 来配置路由转发，biancheng 是自定义的名称，当访问 biancheng/** 开始的地址时，就会跳转到 [`c.biancheng.net`](http://c.biancheng.net) 上。比如输入地址为“http://localhost:2103/biancheng/abc”，访问结果如图 1 所示。

![访问结果](img/06b9867ff723be236296089dc9f3d9c9.png)
图 1  访问结果

#### 集成 Eureka

通过对上部分的学习，我们已经可以简单地使用 Zuul 进行路由的转发了，在实际使用中我们通常是用 Zuul 来代理请求转发到内部的服务上去，统一为外部提供服务。

内部服务的数量会很多，而且可以随时扩展，我们不可能每增加一个服务就改一次路由的配置，所以也得通过结合 Eureka 来实现动态的路由转发功能。

首先需要添加 Eureka 的依赖，代码如下所示。

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>

启动类不需要修改，因为 @EnableZuulProxy 已经自带了 @EnableDiscoveryClient。只需要在配置文件中增加 Eureka 的地址即可：

eureka.client.serviceUrl.defaultZone=http://zhangsan:123456@localhost:8761/eureka/

重启服务，我们可以通过默认的转发规则来访问 Eureka 中的服务。访问规则是“API 网关地址+访问的服务名称+接口 URI”。

比如访问我们之前在 hystrix-feign-demo 服务中定义的 /callHello 接口，就相当于通过 http://localhost:2103/hystrix-feign-demo/callHello 来访问 hystrix-feign-demo 服务中的 /callHello 接口，如图 2 所示。

![访问结果](img/b344dd1c0387f87f1916f210b902f1da.png)
图 2  访问结果