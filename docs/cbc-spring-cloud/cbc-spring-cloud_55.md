# Spring Cloud 使用 Sleuth 在应用中进行日志跟踪

> 原文：[`c.biancheng.net/view/5493.html`](http://c.biancheng.net/view/5493.html)

首先我们需要在跟踪的服务中集成 Sleuth，所有需要跟踪的服务都加上依赖。

Sleuth Maven 配置代码如下所示。

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>

集成完成之后，我们就可以看效果了，前提是需要有两个以上的服务，比如 A 服务、B 服务，然后 A 服务中调用 B 服务提供的接口，分别在 A 和 B 服务的接口中输出日志。

首先建两个简单的服务，分别为 sleuth-article-service 和 sleuth-user-service。

sleuth-article-service 中的 ArticleController 调用 sleuth-user-service 提供的接口，具体代码如下。

```

@RestController
public class ArticleController {

    private Logger logger = LoggerFactory.getLogger(ArticleController.class);

    @Autowired
    private RestTemplate restTemplate;

    @Autowired
    private ArticleService articleService;

    @Autowired
    Tracer tracer;

    @GetMapping("/article/callHello")
    public String callHello() {
        logger.info("我是/article/callHello");
        tracer.currentSpan().tag("用户", "zhangsan");
        return restTemplate.getForObject("http://sleuth-user-service/user/hello", String.class);
    }
}
```

sleuth-user-service 中的 UserController 调用 sleuth-article-service 提供的接口，具体代码如下所示。

```

@RestController
public class UserController {

    private Logger logger = LoggerFactory.getLogger(UserController.class);

    @GetMapping("/user/hello")
    public String hello() {
        logger.info("我是/user/hello");
        try {
            Thread.sleep(4000);
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        return "hello";
    }
}
```

然后我们访问 sleuth-article-service 服务的接口，查看输出的日志信息，具体如下：

2019-08-28 16:16:10.187  INFO [sleuth-article-service,81bc3db3c143f1fb,81bc3db3c143f1fb,false] 6355 --- [nio-8082-exec-1] c.c.e.controller.ArticleController: 我是/article/callHello

查看 sleuth-user-service 输出的日志信息，具体如下：

2019-08-28 16:16:10.570  INFO [sleuth-user-service,81bc3db3c143f1fb,c0cf0d53ce2a8047,false] 6356 --- [nio-8083-exec-1]c.c.e.controller.UserController: 我是/user/hello

在方法中记录日志，我们会发现在日志的最前面加了一部分内容，这部分内容就是 Sleuth 为服务直接提供的链路信息。

可以看到内容是由 [appname，traceId，spanId，exportable] 组成的，具体含义如下：

*   appname：服务的名称，也就是 spring.application.name 的值。
*   traceId：整个请求的唯一 ID，它标识整个请求的链路。
*   spanId：基本的工作单元，发起一次远程调用就是一个 span。
*   exportable：决定是否导入数据到 Zipkin 中。