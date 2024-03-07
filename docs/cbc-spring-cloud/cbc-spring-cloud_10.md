# SpringCloud 组件:搭建 Eureka 服务注册中心

> 原文：[`c.biancheng.net/view/5321.html`](http://c.biancheng.net/view/5321.html)

首先创建一个 Maven 项目，取名为 eureka-server，在 pom.xml 中配置 Eureka 的依赖信息，代码如下所示。

```

<!-- Spring Boot -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.6.RELEASE</version>
    <relativePath />
</parent>

<dependencies>
    <!-- eureka -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
    </dependency>
</dependencies>

<!-- Spring Cloud -->
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
```

创建一个启动类 EurekaServerApplication，代码如下所示。

```

@EnableEurekaServer
@SpringBootApplication
    public static void main(String[] args) {
        SpringApplication.run(EurekaServer Application.class, args);
    }
}
```

这里所说的启动类，跟我们之前讲的 Spring Boot 几乎完全一样，只是多了一个 @EnableEurekaServer 注解，表示开启 Eureka Server。

接下来在 src/main/resources 下面创建一个 application.properties 属性文件，增加下面的配置：

spring.application.name=eureka-server
server.port=8761
# 由于该应用为注册中心, 所以设置为 false, 代表不向注册中心注册自己
eureka.client.register-with-eureka=false
# 由于注册中心的职责就是维护服务实例, 它并不需要去检索服务, 所以也设置为 false
eureka.client.fetch-registry=false

eureka.client.register-with-eureka 一定要配置为 false，不然启动时会把自己当作客户端向自己注册，会报错。

接下来直接运行 EurekaServerApplication 就可以启动我们的注册中心服务了。我们在 application.properties 配置的端口是 8761，则可以直接通过 http://localhost：8761/ (http://localhost%EF%BC%9A8761/) 去浏览器中访问，然后便会看到 Eureka 提供的 Web 控制台。