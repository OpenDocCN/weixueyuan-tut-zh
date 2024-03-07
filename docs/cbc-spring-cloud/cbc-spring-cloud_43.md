# Spring Cloud Gateway 过滤器工厂的使用

> 原文：[`c.biancheng.net/view/5440.html`](http://c.biancheng.net/view/5440.html)

GatewayFilter Factory 是 Spring Cloud Gateway 中提供的过滤器工厂。Spring Cloud Gateway 的路由过滤器允许以某种方式修改传入的 HTTP 请求或输出的 HTTP 响应，只作用于特定的路由。

Spring Cloud Gateway 中内置了很多过滤器工厂，直接采用配置的方式使用即可，同时也支持自定义 GatewayFilter Factory 来实现更复杂的业务需求。

spring:
  cloud:
    gateway:
      routes:
        - id: add_request_header_route
  uri: http://c.biancheng.net
  filters:
    - AddRequestHeader=X-Request-Foo, Bar

接下来为大家介绍几个常用的过滤器工厂类。

#### 1\. AddRequestHeader 过滤器工厂

通过名称我们可以快速明白这个过滤器工厂的作用是添加请求头。

符合规则匹配成功的请求，将添加 X-Request-Foo：bar 请求头，将其传递到后端服务中，后方服务可以直接获取请求头信息。代码如下所示。

```

@GetMapping("/hello")
public String hello(HttpServletRequest request) throws Exception {
    System.err.println(request.getHeader("X-Request-Foo"));
    return "success";
}
```

#### 2\. RemoveRequestHeader 过滤器工厂

RemoveRequestHeader 是移除请求头的过滤器工厂，可以在请求转发到后端服务之前进行 Header 的移除操作。

spring:
  cloud:
    gateway:
      routes:
  - id: removerequestheader_route
  uri: http://c.biancheng.net
    - RemoveRequestHeader=X-Request-Foo

#### 3\. SetStatus 过滤器工厂

SetStatus 过滤器工厂接收单个状态，用于设置 Http 请求的响应码。它必须是有效的 Spring Httpstatus（org.springframework.http.HttpStatus）。它可以是整数值 404 或枚举类型 NOT_FOUND。

spring:
  cloud:
    gateway:
      routes:
        - id: setstatusint_route
  uri: http://c.biancheng.net
  filters:
    - SetStatus=401

#### 4\. RedirectTo 过滤器工厂

RedirectTo 过滤器工厂用于重定向操作，比如我们需要重定向到百度。

spring:
  cloud:
    gateway:
      routes:
        - id: prefixpath_route
  uri: http://c.biancheng.net
  filters:
    - RedirectTo=302, http://baidu.com

以上为大家介绍了几个过滤器工厂的使用，教程后面还会为大家介绍 Retry 重试、RequestRateLimiter 限流、Hystrix 熔断过滤器工厂等内容，其他的大家可以自行参考官方文档进行学习。

## 自定义 Spring Cloud Gateway 过滤器工厂

自定义 Spring Cloud Gateway 过滤器工厂需要继承 AbstractGatewayFilterFactory 类，重写 apply 方法的逻辑。命名需要以 GatewayFilterFactory 结尾，比如 CheckAuthGatewayFilterFactory，那么在使用的时候 CheckAuth 就是这个过滤器工厂的名称。

自定义过滤器工厂代码如下所示。

```

@Component
public class CheckAuth2GatewayFilterFactory
        extends AbstractGatewayFilterFactory<CheckAuth2GatewayFilterFactory.Config> {

    public CheckAuth2GatewayFilterFactory() {
        super(Config.class);
    }

    @Override
    public GatewayFilter apply(Config config) {
    return (exchange, chain) -> {
      System.err.println("进入了 CheckAuth2GatewayFilterFactory" + config.getName());
      ServerHttpRequest request = exchange.getRequest().mutate()
      .build();
      return
      chain.filter(exchange.mutate().request(request).build());
    }
  }

    public static class Config {
        private String name;

        public void setName(String name) {
            this.name = name;
        }

        public String getName() {
            return name;
        }
    }
}
```

使用如下：

filters:
  - name: CheckAuth2
  args:
    name: 张三

如果你的配置是 Key、Value 这种形式的，那么可以不用自己定义配置类，直接继承 AbstractNameValueGatewayFilterFactory 类即可。

AbstractNameValueGatewayFilterFactory 类继承了 AbstractGatewayFilterFactory，定义了一个 NameValueConfig 配置类，NameValueConfig 中有 name 和 value 两个字段。

我们可以直接使用，AddRequestHeaderGatewayFilterFactory、AddRequestParameterGatewayFilterFactory 等都是直接继承的 AbstractNameValueGatewayFilterFactory。

继承 AbstractNameValueGatewayFilterFactory 方式定义过滤器工厂，代码如下所示。

```

@Component
public class CheckAuthGatewayFilterFactory extends AbstractNameValueGatewayFilter-actory {

    @Override
    public GatewayFilter apply(NameValueConfig config) {
        return (exchange, chain) -> {
            System.err.println("进入了 CheckAuthGatewayFilterFactory" + config.getName() + "\t" + config.getValue());
            ServerHttpRequest request = exchange.getRequest().mutate().build();

            return chain.filter(exchange.mutate().request(request).build());
        };
    }
}
```

使用如下：

filters：
        - CheckAuth=zhangsan,男