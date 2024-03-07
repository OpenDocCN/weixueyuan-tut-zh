# RestTemplate 调用前统一申请 Token 传递到调用的服务中

> 原文：[`c.biancheng.net/view/5507.html`](http://c.biancheng.net/view/5507.html)

如果项目中用的 RestTemplate 来调用服务提供的接口，可以利用 RestTemplate 的拦截器来传递 Token，代码如下所示。

```
@Component
public class TokenInterceptor implements ClientHttpRequestInterceptor {

    @Override
    public ClientHttpResponse intercept(HttpRequest request, byte[] body, ClientHttpRequestExecution execution)
            throws IOException {
        System.err.println("进入 RestTemplate 拦截器");
        HttpHeaders headers = request.getHeaders();
        headers.add("Authorization", System.getProperty("fangjia.auth.token"));
        return execution.execute(request, body);
    }
}
```

将拦截器注入 RestTemplate，代码如下所示。

```
@Configuration
public class BeanConfiguration {

    @Autowired
    private TokenInterceptor tokenInterceptor;

    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate() {
        RestTemplate restTemplate = new RestTemplate();
        restTemplate.setInterceptors(Collections.singletonList(tokenInterceptor));
        return restTemplate;
    }
}
```