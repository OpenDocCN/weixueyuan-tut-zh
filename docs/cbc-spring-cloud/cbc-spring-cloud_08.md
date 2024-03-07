# Spring Boot Starter 的介绍及使用

> 原文：[`c.biancheng.net/view/5318.html`](http://c.biancheng.net/view/5318.html)

Spring Boot 的便利性体现在，它简化了很多烦琐的配置，这对于开发人员来说是一个福音，通过引入各种 Spring Boot Starter 包可以快速搭建出一个项目的脚手架。

目前提供的 Spring Boot Starter 包有：

*   spring-boot-starter-web：快速构建基于 Spring MVC 的 Web 项目，使用 Tomcat 做默认嵌入式容器。
*   spring-boot-starter-data-redis：操作 Redis。
*   spring-boot-starter-data-mongodb：操作 Mongodb。
*   spring-boot-starter-data-jpa：操作 Mysql。
*   spring-boot-starter-activemq：操作 Activemq。
*   ……

自动配置非常方便，当我们要操作 Mongodb 的时候，只需要引入 spring-boot-starter-data-mongodb 的依赖，然后配置 Mongodb 的链接信息 spring.data.mongodb.uri=mongodb：//localhost/test 就可以使用 MongoTemplate 来操作数据，MongoTemplate 的初始化工作全部交给 Starter 来完成。

自动配置麻烦的是当出现错误时，排查问题的难度上升了。自动配置的逻辑都在 Spring Boot Starter 中，要想快速定位问题，就必须得了解 Spring Boot Starter 的内部原理。接下来我们自己动手来实现一个 Spring Boot Starter。

## Spring Boot Starter 项目创建

创建一个项目 spring-boot-starter-demo，pom.xml 配置代码如下所示。

```

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
```

创建一个配置类，用于在属性文件中配置值，相当于 spring.data.mongo 这种形式，代码如下所示。

```

import org.springframework.boot.context.properties.ConfigurationProperties;
import lombok.Data;

@Data
@ConfigurationProperties("spring.user")
public class UserPorperties {
    private String name;
}
```

再定义一个 Client，相当于 MongoTemplate，里面定一个方法，用于获取配置中的值，代码如下所示。

```

public class UserClient {
    private UserPorperties userPorperties;

    public UserClient() {

    }

    public UserClient(UserPorperties p) {
        this.userPorperties = p;
    }

    public String getName() {
        return userPorperties.getName();
    }
}
```

## 自动创建客户端

一个最基本的 Starter 包定义好了，但目前肯定是不能使用 UserClient，因为我们没有自动构建 UserClient 的实例。接下来开始构建 UserClient，代码如下所示。

```

@Configuration
@EnableConfigurationProperties(UserPorperties.class)
public class UserAutoConfigure {

    @Bean
    @ConditionalOnProperty(prefix = "spring.user", value = "enabled", havingValue = "true")
    public UserClient userClient(UserPorperties userPorperties) {
        return new UserClient(userPorperties);
    }
}
```

Spring Boot 会默认扫描跟启动类平级的包，假如我们的 Starter 跟启动类不在同一个主包下，如何能让 UserAutoConfigure 生效？

在 resources 下创建一个 META-INF 文件夹，然后在 META-INF 文件夹中创建一个 spring.factories 文件，文件中指定自动配置的类：

org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.cxytiandi.demo.UserAutoConfigure

Spring Boot 启动时会去读取 spring.factories 文件，然后根据配置激活对应的配置类，至此一个简单的 Starter 包就实现了。

## 使用 Starter

现在可以在其他的项目中引入这个 Starter 包，代码如下所示。

```

<dependency>
    <groupId>com.cxytiandi</groupId>
    <artifactId>spring-boot-starter-demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>
```

引入之后就直接可以使用 UserClient，UserClient 在项目启动的时候已经自动初始化好，代码如下所示。

```

@RestController
public class UserController {

    @Autowired
    private UserClient userClient;

    @GetMapping("/user/name")
    public String getUserName() {
        return userClient.getName();
    }
}
```

属性文件中配置 name 的值和开启 UserClient：

spring.user.name=zhangsan
spring.user.enabled=true

访问 /user/name 就可以返回我们配置的 zhangsan。

## 使用注解开启 Starter 自动构建

很多时候我们不想在引入 Starter 包时就执行初始化的逻辑，而是想要由用户来指定是否要开启 Starter 包的自动配置功能，比如常用的 @EnableAsync 这个注解就是用于开启调用方法异步执行的功能。

同样地，我们也可以通过注解的方式来开启是否自动配置，如果用注解的方式，那么 spring.factories 就不需要编写了，下面就来看一下怎么定义启用自动配置的注解，代码如下所示。

@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Import({UserAutoConfigure.class})
public @interface EnableUserClient {

}

这段代码的核心是 @Import（{UserAutoConfigure.class}），通过导入的方式实现把 UserAutoConfigure 实例加入 SpringIOC 容器中，这样就能开启自动配置了。

使用方式就是在启动类上加上该注解，代码如下所示。

```

@SpringBootApplication
public class SpringBootDemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringBootDemoApplication.class, args);
    }
}
```

## 使用配置开启 Starter 自动构建

在某些场景下，UserAutoConfigure 中会配置多个对象，对于这些对象，如果不想全部配置，或是想让用户指定需要开启配置的时候再去构建对象，这个时候我们可以通过 @ConditionalOnProperty 来指定是否开启配置的功能，代码如下所示。

```

@Bean
@ConditionalOnProperty(prefix = "spring.user",value = "enabled",havingValue = "true")
public UserClient userClient(UserPorperties userPorperties) {
    return new UserClient(userPorperties);
}
```

通过上面的配置，只有当启动类加了 @EnableUserClient 并且配置文件中 spring.user.enabled=true 的时候才会自动配置 UserClient。

## 配置 Starter 内容提示

在自定义 Starter 包的过程中，还有一点比较重要，就是对配置的内容项进行提示，需要注意的是，Eclipse 中是不支持提示的，Spring Tools 4 for Eclipse 中可以提示。

定义提示内容需要在 META-INF 中创建一个 spring-configuration-metadata.json 文件，代码如下所示。

{ "properties": [ { "name": "spring.user.name", "defaultValue": "cxytinadi" }, { "name": "spring.user.enabled", "type": "java.lang.Boolean", "defaultValue": false } ] }

*   name：配置名
*   type：配置的数据类型
*   defaultValue：默认值