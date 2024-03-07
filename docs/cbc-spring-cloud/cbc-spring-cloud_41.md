# Spring Cloud Gateway 整合 Eureka 路由转发

> 原文：[`c.biancheng.net/view/5436.html`](http://c.biancheng.net/view/5436.html)

本节我们先创建一个 Gateway 项目，然后实现了一个最简单的转发功能，并进行 Eureka 路由的整合。

## 创建 Gateway 项目

创建一个 Spring Boot 的 Maven 项目，增加 Spring Cloud Gateway 的依赖，代码如下所示。

```

<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.6.RELEASE</version>
    <relativePath />
</parent>
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>Finchley.SR2</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-gateway</artifactId>
    </dependency>
</dependencies>
```

启动类就按 Spring Boot 的方式即可，无须添加额外的注解。代码如下所示。

```

@SpringBootApplication
public class App {
    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}
```

## 路由转发示例

下面来实现一个最简单的转发功能——基于 Path 的匹配转发功能。

Gateway 的路由配置对 yml 文件支持比较好，我们在 resources 下建一个 application.yml 的文件，内容如下：

server:
  port: 2001
spring:
  cloud:
    gateway:
      routes:
        - id: path_route
uri: http://c.biancheng.net
predicates:
  - Path=/spring_cloud

当你访问 http://localhost:2001/spring_cloud 的时候就会转发到 http://c.biancheng.net/spring_cloud。

如果我们要支持多级 Path，配置方式跟 Zuul 中一样，在后面加上两个`*`号即可，比如：

- id: path_route2
uri: http://c.biancheng.net
predicates:
  - Path=/spring_cloud/**

这样一来，上面的配置就可以支持多级 Path，比如访问 http://localhost:2001/spring_cloud/view/1 的时候就会转发到 http://c.biancheng.net/spring_cloud/view/1。

## 整合 Eureka 路由

添加 Eureka Client 的依赖，代码如下所示。

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>

配置基于 Eureka 的路由：

- id: user-service
uri: lb://user-service
predicates:
  - Path=/user-service/**

uri 以`lb：//`开头（lb 代表从注册中心获取服务），后面接的就是你需要转发到的服务名称，这个服务名称必须跟 Eureka 中的对应，否则会找不到服务，错误代码如下：

org.springframework.cloud.gateway.support.NotFoundException: Unable to find instance for user-service1

## 整合 Eureka 的默认路由

Zuul 默认会为所有服务都进行转发操作，我们只需要在访问路径上指定要访问的服务即可，通过这种方式就不用为每个服务都去配置转发规则，当新加了服务的时候，不用去配置路由规则和重启网关。

在 Spring Cloud Gateway 中当然也有这样的功能，通过配置即可开启，配置如下：

spring:
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true

开启之后我们就可以通过地址去访问服务了，格式如下：

http://网关地址/服务名称(大写)/**
http://localhost:2001/USER-SERVICE/user/get?id=1

这个大写的名称还是有很大的影响，如果我们从 Zuul 升级到 Spring Cloud Gateway 的话意味着请求地址有改变，或者重新配置每个服务的路由地址，通过源码笔者发现可以做到兼容处理，再增加一个配置即可：

spring:
  cloud:
    gateway:
      discovery:
        locator:
          lowerCaseServiceId: true

配置完成之后我们就可以通过小写的服务名称进行访问了，如下所示：

http://网关地址/服务名称(小写)/**
http://localhost:2001/user-service/user/get?id=1

注意：开启小写服务名称后大写的服务名称就不能使用，两者只能选其一。

配置源码在 org.springframework.cloud.gateway.discovery.DiscoveryLocatorProperties 类中，代码所示。

```

@ConfigurationProperties("spring.cloud.gateway.discovery.locator")
public class DiscoveryLocatorProperties {
    /**
     * 服务名称小写配置, 默认为 false
     *
     */
    private boolean lowerCaseServiceId = false;
}
```