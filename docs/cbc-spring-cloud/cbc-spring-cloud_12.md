# 使用 Eureka 编写服务消费者

> 原文：[`c.biancheng.net/view/5324.html`](http://c.biancheng.net/view/5324.html)

本节主要讲解如何使用 Eureka 编写服务消费者。

#### 1）直接调用接口

创建服务消费者，消费我们刚刚编写的 user/hello 接口，同样需要先创建一个 Maven 项目 eureka-client-article-service，然后添加依赖，依赖和服务提供者的一样，这里就不贴代码了。

创建启动类 App，启动代码与前面所讲也是一样的。唯一不同的就是 application.properties 文件中的配置信息：

spring.application.name=eureka-client-article-service
server.port=8082

RestTemplate 是 Spring 提供的用于访问 Rest 服务的客户端，RestTemplate 提供了多种便捷访问远程 Http 服务的方法，能够大大提高客户端的编写效率。我们通过配置 RestTemplate 来调用接口，代码如下所示。

```

@Configuration
public class BeanConfiguration {
    @Bean
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
}
```

创建接口，在接口中调用 user/hello 接口，代码如下所示。

```

@RestController
public class ArticleController {
    @Autowired
    private RestTemplate restTemplate;

    @GetMapping("/article /callHello")
    public String callHello() {
        return restTemplate.getForObject("http://localhost:8081/user/hello", String.class);
    }
}
```

执行 App 启动消费者服务，访问 /article/callHello 接口来看看有没有返回 Hello 字符串，如果返回了就证明调用成功。访问地址为 http://localhost:8082/article/callHello (http://localhost%EF%BC%9A8082/article/callHello)。

#### 2）通过 Eureka 来消费接口

上面提到的方法是直接通过服务接口的地址来调用的，和我们之前的做法一样，完全没有用到 Eureka 带给我们的便利。既然用了注册中心，那么客户端调用的时候肯定是不需要关心有多少个服务提供接口，下面我们来改造之前的调用代码。

首先改造 RestTemplate 的配置，添加一个 @LoadBalanced 注解，这个注解会自动构造 LoadBalancerClient 接口的实现类并注册到 Spring 容器中，代码如下所示。

```

@Configuration
public class BeanConfiguration {
    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
}
```

接下来就是改造调用代码，我们不再直接写固定地址，而是写成服务的名称，这个名称就是我们注册到 Eureka 中的名称，是属性文件中的 spring.application.name，相关代码如下所示。

```

@GetMapping("/article/callHello2")
public String callHello2() {
    return restTemplate.getForObject("http://eureka-client-user-service/user/hello", String.class);
}
```