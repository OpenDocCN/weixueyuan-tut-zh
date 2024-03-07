# Spring Boot Admin 集成 Eureka 项目搭建

> 原文：[`c.biancheng.net/view/5520.html`](http://c.biancheng.net/view/5520.html)

通过《Spring Boot Admin 的介绍及使用》教程的学习，已经可以在 Spring Boot Admin 中查看应用中 Actuator 的监控信息了，但是这种方式有一点不好的地方，就是每个被监控的服务都必须配置 Spring Boot Admin 的地址，还得引入依赖。

本节我们将 Spring Boot Admin 也注册到 Eureka 中，然后自动获取 Eureka 中注册的服务信息来统一查看。

将之前 spring-boot-admin 项目复制一份，重命名为 spring-boot-admin-eureka，增加 Eureka 的依赖，代码如下所示。

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>

在启动类上增加 @EnableDiscoveryClient 注解开启注册功能，代码如下所示。

```

@EnableDiscoveryClient
@EnableAdminServer
@SpringBootApplication
public class App {
    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}
```

配置 Eureka 注册信息：

eureka.client.serviceUrl.defaultZone=http://zhangsan:123456@localhost:8761/eureka/
eureka.instance.preferIpAddress=true
eureka.instance.instance-id=${spring.application.name}:${spring.cloud.client.ipAddress}:${server.port}
eureka.instance.status-page-url=http://${spring.cloud.client.ipAddress}:${server.port}

重启服务即可，Spring Boot Admin 会监控 Eureka 中的所有服务。之前在监控服务中配置的 admin 的 url 和 client 包的依赖都可以去掉了，这种方式整合 Eureka 的方式更简单、方便。

Spring Boot Admin 本身也会注册到 Eureka，在监控列表中当然也包括对自身监控，可以暴露所有端点信息，不然在页面中无法查看监控数据：

management.endpoints.web.exposure.include=*