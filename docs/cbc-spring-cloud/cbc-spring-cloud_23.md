# Spring Cloud 使用 Feign 调用服务接口

> 原文：[`c.biancheng.net/view/5357.html`](http://c.biancheng.net/view/5357.html)

JAVA 项目中接口调用怎么做？

#### 1）Httpclient

HttpClient 是 Apache Jakarta Common 下的子项目，用来提供高效的、最新的、功能丰富的支持 Http 协议的客户端编程工具包，并且它支持 HTTP 协议最新版本和建议。

HttpClient 相比传统 JDK 自带的 URLConnection，提升了易用性和灵活性，使客户端发送 HTTP 请求变得容易，提高了开发的效率。

#### 2）Okhttp

一个处理网络请求的开源项目，是安卓端最火的轻量级框架，由 Square 公司贡献，用于替代 HttpUrlConnection 和 Apache HttpClient。OkHttp 拥有简洁的 API、高效的性能，并支持多种协议（HTTP/2 和 SPDY）。

#### 3）HttpURLConnection

HttpURLConnection 是 Java 的标准类，它继承自 URLConnection，可用于向指定网站发送 GET 请求、POST 请求。HttpURLConnection 使用比较复杂，不像 HttpClient 那样容易使用。

#### 4）RestTemplate

RestTemplate 是 Spring 提供的用于访问 Rest 服务的客户端，RestTemplate 提供了多种便捷访问远程 HTTP 服务的方法，能够大大提高客户端的编写效率。

上面介绍的是最常见的几种调用接口的方法，我们下面要介绍的方法比上面的更简单、方便，它就是 Feign。

Feign 是一个声明式的 REST 客户端，它能让 REST 调用更加简单。Feign 供了 HTTP 请求的模板，通过编写简单的接口和插入注解，就可以定义好 HTTP 请求的参数、格式、地址等信息。

而 Feign 则会完全代理 HTTP 请求，我们只需要像调用方法一样调用它就可以完成服务请求及相关处理。

Spring Cloud 对 Feign 进行了封装，使其支持 SpringMVC 标准注解和 HttpMessageConverters。Feign 可以与 Eureka 和 Ribbon 组合使用以支持负载均衡。

## 在 Spring Cloud 中集成 Feign

在 Spring Cloud 中集成 Feign 的步骤相当简单，首先还是加入 Feign 的依赖，代码如下所示。

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>

在启动类上加 @EnableFeignClients 注解，如果你的 Feign 接口定义跟你的启动类不在同一个包名下，还需要制定扫描的包名 @EnableFeignClients（basePackages=“com.fangjia.api.client”），代码如下所示。

```

@SpringBootApplication
@EnableDiscoveryClient
@EnableFeignClients(basePackages = "com.fangjia.api.client")
public class FshSubstitutionServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(FshSubstitutionServiceApplication.class, args);
    }
}
```

## 使用 Feign 调用接口

定义一个 Feign 的客户端，以接口形式存在，代码如下所示。

```

@FeignClient(value = "eureka-client-user-service")
public interface UserRemoteClient {

    @GetMapping("/user/hello")
    String hello();
}
```

首先我们来看接口上加的 @FeignClient 注解。这个注解标识当前是一个 Feign 的客户端，value 属性是对应的服务名称，也就是你需要调用哪个服务中的接口。

定义方法时直接复制接口的定义即可，当然还有另一种做法，就是将接口单独抽出来定义，然后在 Controller 中实现接口。

在调用的客户端中也实现了接口，从而达到接口共用的目的。我这里的做法是不共用的，即单独创建一个 API Client 的公共项目，基于约定的模式，每写一个接口就要对应写一个调用的 Client，后面打成公共的 jar，这样无论是哪个项目需要调用接口，只要引入公共的接口 SDK jar 即可，不用重新定义一遍了。

定义之后可以直接通过注入 UserRemoteClient 来调用，这对于开发人员来说就像调用本地方法一样。

接下来采用 Feign 来调用 /user/hello 接口，代码如下所示。

```

@Autowired
private UserRemoteClient userRemoteClient;

@GetMapping("/callHello")
public String callHello() {
    //return restTemplate.getForObject("http://localhost:8083/house/hello",String.class);
    //String result = restTemplate.getForObject("http://eureka-client-user-service/user/hello",String.class);
    String result = userRemoteClient.hello();
    System.out.println("调用结果：" + result);
    return result;
}

```

通过跟注释掉的代码相比可以发现，我们的调用方式变得越来越简单了，从最开始的指定地址，到后面通过 Eureka 中的服务名称来调用，再到现在直接通过定义接口来调用。