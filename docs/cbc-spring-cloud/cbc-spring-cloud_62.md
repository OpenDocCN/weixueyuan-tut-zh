# Feign 调用前统一申请 Token 传递到调用的服务中

> 原文：[`c.biancheng.net/view/5506.html`](http://c.biancheng.net/view/5506.html)

如果项目中用的是 HttpClient 或者 RestTemplate 之类的调用接口，则可以在调用之前申请 Token，然后将其塞到请求头中。

在 Spring Cloud 中消费接口肯定是用 Feign 来做的，这意味着我们需要对 Feign 进行改造，需要往请求头中塞上我们申请好的 Token。

#### 1\. 定义请求拦截器

对于 Token 的传递操作，最好在框架层面进行封装，对使用者透明，这样不影响业务代码，但要求通用性一定要强。我们可以定义一个 Feign 的请求拦截器来统一添加请求头信息，代码如下所示。

```

/**
* Feign 请求拦截器
**/
public class FeignBasicAuthRequestInterceptor implements RequestInterceptor {
    public FeignBasicAuthRequestInterceptor() {
    }

    @Override
    public void apply(RequestTemplate template) {
        template.header("Authorization", System.getProperty("fangjia.auth.token"));
    }
}
```

#### 2\. 配置拦截器

拦截器需要在 Feign 的配置中定义，代码如下所示。

```

@Configuration
public class FeignConfiguration {
    /**
     * 日志级别
     *
     * @return
     */
    @Bean
    Logger.Level feignLoggerLevel() {
        return Logger.Level.FULL;
    }

    /**
     * 创建 Feign 请求拦截器, 在发送请求前设置认证的 Token, 各个微服务将 Token 设置 到环境变量中来达到通用的目的
     *
     * @return
     */
    @Bean
    public FeignBasicAuthRequestInterceptor basicAuthRequestInterceptor() {
        return new FeignBasicAuthRequestInterceptor();
    }
}
```

上面的准备好之后，我们只需要在调用业务接口之前先调用认证接口，然后将获取到的 Token 设置到环境变量中，通过 System.setProperty("fangjia.auth.token",token) 设置值，或者通过定时任务刷新设置。

这样我们就可以通过 System.setProperty("fangjia.auth.token",token) 获取到需要传递的 Token。