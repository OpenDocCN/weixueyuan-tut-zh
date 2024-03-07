# Spring Cloud Gateway 实战案例（限流、熔断回退、跨域、统一异常处理和重试机制）

> 原文：[`c.biancheng.net/view/5447.html`](http://c.biancheng.net/view/5447.html)

Spring Cloud Gateway 作为新一代网关，在性能上有很大提升，并且附加了诸如限流等实用的功能。本节主要讲解 Gateway 的一些实用功能的实例。

## 限流实战

开发高并发系统时有三把利器用来保护系统：缓存、降级和限流。API 网关作为所有请求的入口，请求量大，我们可以通过对并发访问的请求进行限速来保护系统的可用性。

目前限流提供了基于 Redis 的实现，我们需要增加对应的依赖，代码如下所示。

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
</dependency>

我们可以通过 KeyResolver 来指定限流的 Key，比如我们需要根据用户来做限流，或是根据 IP 来做限流等。

#### 1\. IP 限流

IP 限流的 Key 指定具体代码如下所示。

```

@Bean
public KeyResolver ipKeyResolver() {
    return exchange -> Mono.just(exchange.getRequest().getRemoteAddress().getHostName());
}

public static String getIpAddr(ServerHttpRequest request) {
    HttpHeaders headers = request.getHeaders();
    List<String> ips = headers.get("X-Forwarded-For");
    String ip = "192.168.1.1";
    if (ips != null && ips.size() > 0) {
        ip = ips.get(0);
    }
    return ip;
}
```

#### 2\. 用户限流

根据用户来做限流只需要获取当前请求的用户 ID 或者用户名，代码如下所示。

```

@Bean
KeyResolver userKeyResolver() {
    return exchange ->
        Mono.just(exchange.getRequest().getQueryParams().getFirst("userId"));
}
```

#### 3\. 接口限流

获取请求地址的 uri 作为限流 Key，代码如下所示。

```

@Bean
KeyResolver apiKeyResolver() {
    return exchange ->
        Mono.just(exchange.getRequest().getPath().value());
}
```

然后配置限流的过滤器信息：

server:
  port: 8084
spring:
  redis:
    host: 127.0.0.1
    port: 6379
  cloud:
    gateway:
  routes:
    - id: fsh-house
  uri: lb://fsh-house
  predicates:
    - Path=/house/**
  filters:
    - name: RequestRateLimiter
  args:
    redis-rate-limiter.replenishRate: 10
    redis-rate-limiter.burstCapacity: 20
    key-resolver: "#{@ipKeyResolver}"

*   filter 名称必须是 RequestRateLimiter。
*   redis-rate-limiter.replenishRate：允许用户每秒处理多少个请求。
*   redis-rate-limiter.burstCapacity：令牌桶的容量，允许在 1s 内完成的最大请求数。
*   key-resolver：使用 SpEL 按名称引用 bean。

可以访问接口进行测试，这时候 Redis 中会有对应的数据：

127.0.0.1:6379> keys *
1) "request_rate_limiter.{localhost}.timestamp"
2) "request_rate_limiter.{localhost}.tokens"

大括号中就是我们的限流 Key，这里是 IP，本地的就是 localhost。

*   timestamp：存储的是当前时间的秒数，也就是 System.currentTimeMillis()/1000 或者 Instant.now().getEpochSecond()。
*   tokens：存储的是当前这秒钟对应的可用令牌数量。

## 熔断回退实战

在 Spring Cloud Gateway 中使用 Hystrix 进行回退需要增加 Hystrix 的依赖，代码如下所示。

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>

内置了 HystrixGatewayFilterFactory 来实现路由级别的熔断，只需要配置即可实现熔断回退功能。配置方式如下所示。

- id: user-service
uri: lb://user-service
predicates:
  - Path=/user-service/**
filters:
  - name: Hystrix
args:
  name: fallbackcmd
fallbackUri: forward:/fallback

上面配置了一个 Hystrix 过滤器，该过滤器会使用 Hystrix 熔断与回退，原理是将请求包装成 RouteHystrixCommand 执行，RouteHystrixCommand 继承于 com.netflix.hystrix.HystrixObservableCommand。

fallbackUri 是发生熔断时回退的 URI 地址，目前只支持 forward 模式的 URI。如果服务被降级，该请求会被转发到该 URI 中。

在网关中创建一个回退的接口，用于熔断时处理返回给调用方的信息，代码如下所示。

```

@RestController
public class FallbackController {

    @GetMapping("/fallback")
    public String fallback() {
        return "fallback";
    }
}
```

## 跨域实战

在 Spring Cloud Gateway 中配置跨域有两种方式，分别是代码配置方式和配置文件方式。

代码配置方式配置跨域，具体代码如下所示。

```

@Configuration
public class CorsConfig {

    @Bean
    public WebFilter corsFilter() {
        return (ServerWebExchange ctx, WebFilterChain chain) -> {
            ServerHttpRequest request = ctx.getRequest();
            if (CorsUtils.isCorsRequest(request)) {
                HttpHeaders requestHeaders = request.getHeaders();
                ServerHttpResponse response = ctx.getResponse();
                HttpMethod requestMethod = requestHeaders.getAccessControlRequestMethod();
                HttpHeaders headers = response.getHeaders();
                headers.add(HttpHeaders.ACCESS_CONTROL_ALLOW_ORIGIN, requestHeaders.getOrigin());
                headers.addAll(HttpHeaders.ACCESS_CONTROL_ALLOW_HEADERS,
                        requestHeaders.getAccessControlRequestHeaders());
                if (requestMethod != null) {
                    headers.add(HttpHeaders.ACCESS_CONTROL_ALLOW_METHODS, requestMethod.name());
                }

                headers.add(HttpHeaders.ACCESS_CONTROL_ALLOW_CREDENTIALS, "true");
                headers.add(HttpHeaders.ACCESS_CONTROL_EXPOSE_HEADERS, "*");
                if (request.getMethod() == HttpMethod.OPTIONS) {
                    response.setStatusCode(HttpStatus.OK);
                    return Mono.empty();
                }
            }
            return chain.filter(ctx);
        };
    }
}
```

配置文件方式配置跨域：

spring:
  cloud:
    gateway:
      globalcors:
        corsConfigurations:
          '[/**]':
            allowedOrigins: "*"
            exposedHeaders:
              - content-type
            allowedHeaders:
              - content-type
            allowCredentials: true
              allowedMethods:
              - GET
              - OPTIONS
              - PUT
              - DELETE
              - POST

## 统一异常处理

Spring Cloud Gateway 中的全局异常处理不能直接使用 @ControllerAdvice，可以通过跟踪异常信息的抛出，找到对应的源码，自定义一些处理逻辑来匹配业务的需求。

网关是给接口做代理转发的，后端对应的是 REST API，返回数据格式是 JSON。如果不做处理，当发生异常时，Gateway 默认给出的错误信息是页面，不方便前端进行异常处理。

所以我们需要对异常信息进行处理，并返回 JSON 格式的数据给客户端。下面先看实现的代码，后面再跟大家讲一下需要注意的地方。

自定义异常处理逻辑，代码如下所示。

```

public class JsonExceptionHandler extends DefaultErrorWebExceptionHandler {

    public JsonExceptionHandler(ErrorAttributes errorAttributes, ResourceProperties resourceProperties,
            ErrorProperties errorProperties, ApplicationContext applicationContext) {
        super(errorAttributes, resourceProperties, errorProperties, applicationContext);
    }

    /**
     * 获取异常属性
     */
    @Override
    protected Map<String, Object> getErrorAttributes(ServerRequest request, boolean includeStackTrace) {
        int code = 500;
        Throwable error = super.getError(request);
        if (error instanceof org.springframework.cloud.gateway.support.NotFoundException) {
            code = 404;
        }
        return response(code, this.buildMessage(request, error));
    }

    /**
     * 指定响应处理方法为 JSON 处理的方法
     *
     * @param errorAttributes
     */
    @Override
    protected RouterFunction<ServerResponse> getRoutingFunction(ErrorAttributes errorAttributes) {
        return RouterFunctions.route(RequestPredicates.all(), this::renderErrorResponse);
    }

    /**
     * 根据 code 获取对应的 HttpStatus
     *
     * @param errorAttributes
     */
    @Override
    protected HttpStatus getHttpStatus(Map<String, Object> errorAttributes) {
        int statusCode = (int) errorAttributes.get("code");
        return HttpStatus.valueOf(statusCode);
    }

    /**
     * 构建异常信息
     *
     * @param request
     * @param ex
     * @return
     */
    private String buildMessage(ServerRequest request, Throwable ex) {
        StringBuilder message = new StringBuilder("Failed to handle request [");
        message.append(request.methodName());
        message.append(" ");
        message.append(request.uri());
        message.append("]");
        if (ex != null) {
            message.append(": ");
            message.append(ex.getMessage());
        }
        return message.toString();
    }

    /**
     * 构建返回的 JSON 数据格式
     *
     * @param status       状态码
     * @param errorMessage 异常信息
     * @return
     */
    public static Map<String, Object> response(int status, String errorMessage) {
        Map<String, Object> map = new HashMap<>();
        map.put("code", status);
        map.put("message", errorMessage);
        map.put("data", null);
        return map;
    }
}
```

覆盖默认的配置，代码如下所示。

```

@Configuration
@EnableConfigurationProperties({ ServerProperties.class, ResourceProperties.class })

public class ErrorHandlerConfiguration {
    private final ServerProperties serverProperties;
    private final ApplicationContext applicationContext;
    private final ResourceProperties resourceProperties;
    private final List<ViewResolver> viewResolvers;
    private final ServerCodecConfigurer serverCodecConfigurer;

    public ErrorHandlerConfiguration(ServerProperties serverProperties, ResourceProperties resourceProperties,
            ObjectProvider<List<ViewResolver>> viewResolversProvider, ServerCodecConfigurer serverCodecConfigurer,
            ApplicationContext applicationContext) {
        this.serverProperties = serverProperties;
        this.applicationContext = applicationContext;
        this.resourceProperties = resourceProperties;
        this.viewResolvers = viewResolversProvider.getIfAvailable(Collections::emptyList);
        this.serverCodecConfigurer = serverCodecConfigurer;
    }

    @Bean
    @Order(Ordered.HIGHEST_PRECEDENCE)
    public ErrorWebExceptionHandler errorWebExceptionHandler(ErrorAttributes errorAttributes) {
        JsonExceptionHandler exceptionHandler = new JsonExceptionHandler(errorAttributes, 
            this.resourceProperties,this.serverProperties.getError(), this.applicationContext);
        exceptionHandler.setViewResolvers(this.viewResolvers);
        exceptionHandler.setMessageWriters(this.serverCodecConfigurer.getWriters());
        exceptionHandler.setMessageReaders(this.serverCodecConfigurer.getReaders());
        return exceptionHandler;
    }
}
```

#### 1\. 异常时如何返回 JSON 而不是 HTML？

在 org.springframework.boot.autoconfigure.web.reactive.error.DefaultErrorWeb-Exception-Handler 中的 getRoutingFunction() 方法就是控制返回格式的，源代码如下所示。

@Override
protected RouterFunction<ServerResponse> getRoutingFunction(ErrorAttributes errorAttributes) {
    return RouterFunctions.route(acceptsTextHtml(), this::renderErrorView).andRoute(RequestPredicates.all(), this::renderErrorResponse);
}

这里优先是用 HTML 来显示的，如果想用 JSON 显示改动就可以了，具体代码如下所示。

protected RouterFunction<ServerResponse> getRoutingFunction(ErrorAttributes errorAttributes) {
    return RouterFunctions.route(RequestPredicates.all(),this::renderErrorResponse);
}

#### 2\. getHttpStatus 需要重写

原始的方法是通过 status 来获取对应的 HttpStatus 的，具体代码如下所示。

protected HttpStatus getHttpStatus(Map<String, Object> errorAttributes) {
    int statusCode = (int) errorAttributes.get("status");
    return HttpStatus.valueOf(statusCode);
}

如果我们定义的格式中没有 status 字段的话，就会报错，因为找不到对应的响应码。要么返回数据格式中增加 status 子段，要么重写，在笔者的操作中返回的是 code，所以要重写，代码如下所示。

@Override
protected HttpStatus getHttpStatus(Map<String, Object> errorAttributes) {
    int statusCode = (int) errorAttributes.get("code");
    return HttpStatus.valueOf(statusCode);
}

## 重试机制

RetryGatewayFilter 是 Spring Cloud Gateway 对请求重试提供的一个 GatewayFilter Factory。配置方式如下所示。

spring:
  cloud:
    gateway:
      routes:
        - id: zuul-encrypt-service
  uri: lb://zuul-encrypt-service
  predicates:
    - Path=/data/**
  filters:
    - name: Retry
  args:
    retries: 3
    series: SERVER_ERROR

上述代码中具体参数含义如下所示。

*   retries：重试次数，默认值是 3 次。
*   series：状态码配置（分段），符合某段状态码才会进行重试逻辑，默认值是 SERVER_ERROR，值是 5，也就是 5XX（5 开头的状态码），共有 5 个值，代码如下所示。

public enum Series {
    INFORMATIONAL(1), SUCCESSFUL(2), REDIRECTION(3), CLIENT_ERROR(4), SERVER_ERROR(5);
}

上述代码中具体参数含义如下所示。

*   statuses：状态码配置，和 series 不同的是这里是具体状态码的配置，取值请参考 org.springframework.http.HttpStatus。
*   methods：指定哪些方法的请求需要进行重试逻辑，默认值是 GET 方法，取值代码如下所示。

public enum HttpMethod {
    GET, HEAD, POST, PUT, PATCH, DELETE, OPTIONS, TRACE;
}

上述代码中具体参数含义如下所示。 exceptions：指定哪些异常需要进行重试逻辑。默认值是 java.io.IOException 和 org.springframework.cloud.gateway.support.TimeoutException。