# Spring Cloud Ribbon 结合 RestTemplate 实现负载均衡

> 原文：[`c.biancheng.net/view/5348.html`](http://c.biancheng.net/view/5348.html)

在《Spring Cloud Ribbon 介绍及使用》教程中我们简单地使用 Ribbon 进行了负载的一个调用，这意味着 Ribbon 是可以单独使用的。

在 Spring Cloud 中使用 Ribbon 会更简单，因为 Spring Cloud 在 Ribbon 的基础上进行了一层封装，将很多配置都集成好了。本节将在 Spring Cloud 项目中使用 Ribbon。

## 使用 RestTemplate 与整合 Ribbon

Spring 提供了一种简单便捷的模板类来进行 API 的调用，那就是 RestTemplate。

#### 1\. 使用 RestTemplate

在前面介绍 Eureka 时，我们已经使用过 RestTemplate 了，本节会更加详细地跟大家讲解 RestTemplate 的具体使用方法。

首先我们来看看 GET 请求的使用方式：创建一个新的项目 spring-rest-template，配置好 RestTemplate：

```

@Configuration
public class BeanConfiguration {

    @Bean
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
}
```

新建一个 HouseController，并增加两个接口，一个通过 @RequestParam 来传递参数，返回一个对象信息；另一个通过 @PathVariable 来传递参数，返回一个字符串。请尽量通过两个接口组装不同的形式，具体代码如下所示。

```

@GetMapping("/house/data")
public HouseInfo getData(@RequestParam("name") String name) {
    return new HouseInfo(1L, "上海" "虹口" "东体小区");
}

@GetMapping("/house/data/{name}")
public String getData2(@PathVariable("name") String name) {
    return name;
}
```

新建一个 HouseClientController 用于测试，使用 RestTemplate 来调用我们刚刚定义的两个接口，代码如下所示。

```

@GetMapping("/call/data")
public HouseInfo getData(@RequestParam("name") String name) {
    return restTemplate.getForObject( "http://localhost:8081/house/data?name="+ name, HouseInfo.class);
}

@GetMapping("/call/data/{name}")
public String getData2(@PathVariable("name") String name) {
    return restTemplate.getForObject( "http://localhost:8081/house/data/{name}", String.class, name);
}
```

获取数据结果可通过 RestTemplate 的 getForObject 方法（如下代码所示）来实现，此方法有三个重载的实现：

*   url：请求的 API 地址，有两种方式，其中一种是字符串，另一种是 URI 形式。
*   responseType：返回值的类型。
*   uriVariables：PathVariable 参数，有两种方式，其中一种是可变参数，另一种是 Map 形式。

public <T> T getForObject(String url, Class<T> responseType,
                          Object... uriVariables);
public <T> T getForObject(String url, Class<T> responseType,
                          Map<String, ?> uriVariables);
public <T> T getForObject(URI url, Class<T> responseType);

除了 getForObject，我们还可以使用 getForEntity 来获取数据，代码如下所示。

```

@GetMapping("/call/dataEntity")
public HouseInfo getData(@RequestParam("name") String name) {
    ResponseEntity<HouseInfo> responseEntity = restTemplate
            .getForEntity("http://localhost:8081/house/data?name=" + name, HouseInfo.class);
    if (responseEntity.getStatusCodeValue() == 200) {
        return responseEntity.getBody();
    }
    return null;
}
```

getForEntity 中可以获取返回的状态码、请求头等信息，通过 getBody 获取响应的内容。其余的和 getForObject 一样，也是有 3 个重载的实现。

接下来看看怎么使用 POST 方式调用接口。在 HouseController 中增加一个 save 方法用来接收 HouseInfo 数据，代码如下所示。

```

@PostMapping("/house/save")
public Long addData(@RequestBody HouseInfo houseInfo) {
    System.out.println(houseInfo.getName());
    return 1001L;
}
```

接着写调用代码，用 postForObject 来调用，代码如下所示。

```

@GetMapping("/call/save")
public Long add() {
    HouseInfo houseInfo = new HouseInfo();
    houseInfo.setCity("上海");
    houseInfo.setRegion("虹口");
    houseInfo.setName("×××");
    Long id = restTemplate.postForObject("http://localhost:8081/house/save", houseInfo, Long.class);
    return id;
}
```

postForObject 同样有 3 个重载的实现。除了 postForObject 还可以使用 postForEntity 方法，用法都一样，代码如下所示。

public <T> T postForObject(String url, Object request,
                           Class<T> responseType, Object... uriVariables);

public <T> T postForObject(String url, Object request,
                           Class<T> responseType, Map<String, ?> uriVariables);

public <T> T postForObject(URI url, Object request, Class<T> responseType);

除了 get 和 post 对应的方法之外，RestTemplate 还提供了 put、delete 等操作方法，还有一个比较实用的就是 exchange 方法。exchange 可以执行 get、post、put、delete 这 4 种请求方式。更多地使用方式大家可以自行学习。

#### 2\. 整合 Ribbon

在 Spring Cloud 项目中集成 Ribbon 只需要在 pom.xml 中加入下面的依赖即可，其实也可以不用配置，因为 Eureka 中已经引用了 Ribbon，代码如下所示。

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
</dependency>

## RestTemplate 负载均衡示例

前面我们调用接口都是通过具体的接口地址来进行调用，RestTemplate 可以结合 Eureka 来动态发现服务并进行负载均衡的调用。

修改 RestTemplate 的配置，增加能够让 RestTemplate 具备负载均衡能力的注解 @LoadBalanced。代码如下所示。

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

修改接口调用的代码，将 IP+PORT 改成服务名称，也就是注册到 Eureka 中的名称，代码如下所示。

@GetMapping("/call/data")
public HouseInfo getData(@RequestParam("name") String name) {
    return restTemplate.getForObject("http://ribbon-eureka-demo/house/data?name=" + name, HouseInfo.class);
}

接口调用的时候，框架内部会将服务名称替换成具体的服务 IP 信息，然后进行调用。

## @LoadBalanced 注解原理

相信大家一定有一个疑问：为什么在 RestTemplate 上加了一个 @LoadBalanced 之后，RestTemplate 就能够跟 Eureka 结合了，不但可以使用服务名称去调用接口，还可以负载均衡？

应该归功于 Spring Cloud 给我们做了大量的底层工作，因为它将这些都封装好了，我们用起来才会那么简单。框架就是为了简化代码，提高效率而产生的。

这里主要的逻辑就是给 RestTemplate 增加拦截器，在请求之前对请求的地址进行替换，或者根据具体的负载策略选择服务地址，然后再去调用，这就是 @LoadBalanced 的原理。

下面我们来实现一个简单的拦截器，看看在调用接口之前会不会进入这个拦截器。我们不做任何操作，就输出一句话，证明能进来就行了。具体代码如下所示。

```

public class MyLoadBalancerInterceptor implements ClientHttpRequestInterceptor {

    private LoadBalancerClient loadBalancer;
    private LoadBalancerRequestFactory requestFactory;

    public MyLoadBalancerInterceptor(LoadBalancerClient loadBalancer, LoadBalancerRequestFactory requestFactory) {
        this.loadBalancer = loadBalancer;
        this.requestFactory = requestFactory;
    }

    public MyLoadBalancerInterceptor(LoadBalancerClient loadBalancer) {
        this(loadBalancer, new LoadBalancerRequestFactory(loadBalancer));
    }

    @Override
    public ClientHttpResponse intercept(final HttpRequest request, final byte[] body,
            final ClientHttpRequestExecution execution) throws IOException {
        final URI originalUri = request.getURI();
        String serviceName = originalUri.getHost();
        System.out.println("进入自定义的请求拦截器中" + serviceName);
        Assert.state(serviceName != null, "Request URI does not contain a valid hostname: " + originalUri);

        return this.loadBalancer.execute(serviceName, requestFactory.createRequest(request, body, execution));
    }
}
```

拦截器设置好了之后，我们再定义一个注解，并复制 @LoadBalanced 的代码，改个名称就可以了，代码如下所示。

@Target({ ElementType.FIELD, ElementType.PARAMETER, ElementType.METHOD })
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Qualifier
public @interface MyLoadBalanced {
}

然后定义一个配置类，给 RestTemplate 注入拦截器，代码如下所示。

```

@Configuration
public class MyLoadBalancerAutoConfiguration {
    @MyLoadBalanced
    @Autowired(required = false)
    private List<RestTemplate> restTemplates = Collections.emptyList();

    @Bean
    public MyLoadBalancerInterceptor myLoadBalancerInterceptor() {
        return new MyLoadBalancerInterceptor();
    }

    @Bean
    public SmartInitializingSingleton myLoadBalancedRestTemplateInitializer() {
        return new SmartInitializingSingleton() {
          @Override
          public void afterSingletonsInstantiated() {
            for (RestTemplate restTemplate : MyLoadBalancerAutoConfiguration.this.restTemplates){
                List<ClientHttpRequestInterceptor> list = new ArrayList<>(restTemplate.getInterceptors());
                list.add(myLoad BalancerInterceptor());
                restTemplate.setInterceptors(list);
            }
          }
        };
    }
}
```

维护一个 @MyLoadBalanced 的 RestTemplate 列表，在 SmartInitializingSingleton 中对 RestTemplate 进行拦截器设置。

然后改造我们之前的 RestTemplate 配置，将 @LoadBalanced 改成我们自定义的 @MyLoadBalanced，代码如下所示。

@Bean
//@LoadBalanced
@MyLoadBalanced
public RestTemplate getRestTemplate() {
    return new RestTemplate();
}

重启服务，访问服务中的接口就可以看到控制台的输出了，这证明在接口调用的时候会进入该拦截器，输出如下：

进入自定义的请求拦截器中 ribbon-eureka-demo

通过这个小案例我们就能够清楚地知道 @LoadBalanced 的工作原理。接下来我们来看看源码中是怎样的一个逻辑。

首先看配置类，如何为 RestTemplate 设置拦截器，代码在 spring-cloud-commons.jar 中的 org.springframework.cloud.client.loadbalancer.LoadBalancerAutoConfiguration 类里面通过查看 LoadBalancerAutoConfiguration 的源码，可以看到这里也是维护了一个 @LoadBalanced 的 RestTemplate 列表，代码如下所示。

```

@LoadBalanced
@Autowired(required = false)
private List<RestTemplate> restTemplates = Collections.emptyList();

@Bean
public SmartInitializingSingleton loadBalancedRestTemplateInitializer(final List<RestTemplateCustomizer> customizers) {
    return new SmartInitializingSingleton() {
        @Override
        public void afterSingletonsInstantiated() {
            for (RestTemplate restTemplate : LoadBalancerAutoConfiguration.this.restTemplates) {
                for (RestTemplateCustomizer customizer : customizers) {
                    customizer.customize(restTemplate);
                }
            }
        }
    };
}
```

通过查看拦截器的配置可以知道，拦截器用的是 LoadBalancerInterceptor，RestTemplate Customizer 用来添加拦截器，代码如下所示。

```

@Configuration
@ConditionalOnMissingClass("org.springframework.retry.support.RetryTemplate")
static class LoadBalancerInterceptorConfig {

    @Bean
    public LoadBalancerInterceptor ribbonInterceptor(LoadBalancerClient loadBalancerClient,
            LoadBalancerRequestFactory requestFactory) {
        return new LoadBalancerInterceptor(loadBalancerClient, requestFactory);
    }

    @Bean
    @ConditionalOnMissingBean
    public RestTemplateCustomizer restTemplateCustomizer(
            final LoadBalancerInterceptor loadBalancerInterceptor) {
        return new RestTemplateCustomizer() {
            @Override
            public void customize(RestTemplate restTemplate) {
                List<ClientHttpRequestInterceptor> list = new ArrayList<>(
                  restTemplate.getInterceptors());
                list.add(loadBalancerInterceptor);
                restTemplate.setInterceptors(list);
            }
        };
    }
}
```

拦截器的代码在 org.springframework.cloud.client.loadbalancer.LoadBalancerInterceptor 中，代码如下所示。

```

public class LoadBalancerInterceptor implements ClientHttpRequestInterceptor {
    private LoadBalancerClient loadBalancer;
    private LoadBalancerRequestFactory requestFactory;

    public LoadBalancerInterceptor(LoadBalancerClient loadBalancer, LoadBalancerRequestFactory requestFactory) {
        this.loadBalancer = loadBalancer;
        this.requestFactory = requestFactory;
    }

    public LoadBalancerInterceptor(LoadBalancerClient loadBalancer) {
        this(loadBalancer, new LoadBalancerRequestFactory(loadBalancer));
    }

    @Override
    public ClientHttpResponse intercept(final HttpRequest request, final byte[] body,
            final ClientHttpRequestExecution execution) throws IOException {
        final URI originalUri = request.getURI();
        String serviceName = originalUri.getHost();
        Assert.state(serviceName != null, "Request URI does not contain a valid hostname:" + originalUri);
        return this.loadBalancer.execute(serviceName, requestFactory.createRequest(request, body, execution));
    }
}
```

主要的逻辑在 intercept 中，执行交给了 LoadBalancerClient 来处理，通过 LoadBalancer RequestFactory 来构建一个 LoadBalancerRequest 对象，代码如下所示。

```

public LoadBalancerRequest<ClientHttpResponse> createRequest(final HttpRequest request, final byte[] body,
        final ClientHttpRequestExecution execution) {
    return new LoadBalancerRequest<ClientHttpResponse>() {
        @Override
        public ClientHttpResponse apply(final ServiceInstance instance) throws Exception {
            HttpRequest serviceRequest = new ServiceRequestWrapper(request, instance, loadBalancer);
            if (transformers != null) {
                for (LoadBalancerRequestTransformer transformer : transformers) {
                    serviceRequest = transformer.transformRequest(serviceRequest,instance);
                }
            }
            return execution.execute(serviceRequest, body);
        }
    };
}
```

createRequest 中通过 ServiceRequestWrapper 来执行替换 URI 的逻辑，ServiceRequest Wrapper 中将 URI 的获取交给了 org.springframework.cloud.client.loadbalancer.LoadBalancer Client#reconstructURI 方法。

以上就是整个 RestTemplate 结合 @LoadBalanced 的执行流程，至于具体的实现大家可以自己去研究，这里只介绍原理及整个流程。

## Ribbon API 使用

当你有一些特殊的需求，想通过 Ribbon 获取对应的服务信息时，可以使用 Load-Balancer Client 来获取，比如你想获取一个 ribbon-eureka-demo 服务的服务地址，可以通过 LoadBalancerClient 的 choose 方法来选择一个：

```

@Autowired
private LoadBalancerClient loadBalancer;

@GetMapping("/choose")
public Object chooseUrl() {
    ServiceInstance instance = loadBalancer.choose("ribbon-eureka-demo");
    return instance;
}
```

访问接口，可以看到返回的信息如下：

```

{
    serviceId: "ribbon-eureka-demo",
    server: {
        host: "localhost",
        port: 8081,
        id: "localhost:8081",
        zone: "UNKNOWN",
        readyToServe: true,
        alive: true,
        hostPort: "localhost:8081",
        metaInfo: {
            serverGroup: null,
            serviceIdForDiscovery: null, instanceId: "localhost:8081",
            appName: null
        }
    },
    secure: false, metadata: { }, host: "localhost", port: 8081,
    uri: "http://localhost:8081"
}
```

## Ribbon 饥饿加载

笔者从网上看到很多博客中都提到过的一种情况：在进行服务调用的时候，如果网络情况不好，第一次调用会超时。有很多大神对此提出了解决方案，比如把超时时间改长一点、禁用超时等。

Spring Cloud 目前正在高速发展中，版本更新很快，我们能发现的问题基本上在版本更新的时候就修复了，或者提供最优的解决方案。

超时的问题也是一样，Ribbon 的客户端是在第一次请求的时候初始化的，如果超时时间比较短的话，初始化 Client 的时间再加上请求接口的时间，就会导致第一次请求超时。

本教程是基于 Finchley.SR2 撰写的，这个版本已经提供了一种针对上述问题的解决方法，那就是 eager-load 方式。通过配置 eager-load 来提前初始化客户端就可以解决这个问题。

ribbon.eager-load.enabled=true
ribbon.eager-load.clients=ribbon-eureka-demo

*   ribbon.eager-load.enabled：开启 Ribbon 的饥饿加载模式。
*   ribbon.eager-load.clients：指定需要饥饿加载的服务名，也就是你需要调用的服务，若有多个则用逗号隔开。

怎么进行验证呢？网络情况确实不太好模拟，不过通过调试源码的方式即可验证，在 org.springframework.cloud.netflix.ribbon.RibbonAutoConfiguration 中找到对应的代码，代码如下所示。

@Bean
@ConditionalOnProperty(value = "ribbon.eager-load.enabled")
public RibbonApplicationContextInitializer ribbonApplicationContextInitializer() {
    return new RibbonApplicationContextInitializer(springClientFactory(),ribbonEagerLoadProperties.getClients());
}

在 return 这行设置一个断点，然后以调试的模式启动服务，如果能进入到这个断点的代码这里，就证明配置生效了