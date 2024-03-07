# 使用 Eureka 编写服务提供者

> 原文：[`c.biancheng.net/view/5323.html`](http://c.biancheng.net/view/5323.html)

本节主要讲解如何使用 Eureka 编写服务提供者。

#### 1）创建项目注册到 Eureka

注册中心已经创建并且启动好了，接下来我们实现将一个服务提供者 eureka-client-user-service 注册到 Eureka 中，并提供一个接口给其他服务调用。

首先还是创建一个 Maven 项目，然后在 pom.xml 中增加相关依赖，代码如下所示。

```

<!-- Spring Boot -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.6.RELEASE</version>
    <relativePath />
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- eureka -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
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

创建一个启动类 App，代码如下所示。

```

@SpringBootApplication
@EnableDiscoveryClient
public class App {

    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}
```

启动类的方法与之前没有多大区别，只是注解换成 @EnableDiscoveryClient，表示当前服务是一个 Eureka 的客户端。

接下来在 src/main/resources 下面创建一个 application.properties 属性文件，增加下面的配置：

spring.application.name= eureka-client-user-service
server.port=8081
eureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/
# 采用 IP 注册
eureka.instance.preferIpAddress=true
# 定义实例 ID 格式
eureka.instance.instance-id=${spring.application.name}:${spring.cloud.client.ip-address}:${server.port}

eureka.client.serviceUrl.defaultZone 的地址就是我们之前启动的 Eureka 服务的地址，在启动的时候需要将自身的信息注册到 Eureka 中去。

执行 App 启动服务，我们可以看到控制台中有输出注册信息的日志：

DiscoveryClient_EUREKA-CLIENT-USER-SERVICE/eureka-client-user-service:192.168.31.245:8081 - registration status: 204

我们可以进一步检查服务是否注册成功。回到之前打开的 Eureka 的 Web 控制台，刷新页面，就可以看到新注册的服务信息了。

#### 2）编写提供接口

创建一个 Controller，提供一个接口给其他服务查询，代码如下所示。

```

@RestController
public class UserController {
    @GetMapping("/user/hello")
    public String hello() {
        return “hello”;
    }
}
```

重启服务，访问 http://localhost:8081/user/hello (http://localhost%EF%BC%9A8081/user/hello)，如果能看到我们返回的 Hello 字符串，就证明接口提供成功了。