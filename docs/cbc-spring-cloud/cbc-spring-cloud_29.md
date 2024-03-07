# Spring Cloud Feign 整合 Hystrix 实现容错处理

> 原文：[`c.biancheng.net/view/5374.html`](http://c.biancheng.net/view/5374.html)

创建一个新的 Maven 项目 hystrix-feign-demo，增加 EurekaClient，Feign，Hystrix 的依赖，然后在属性文件中开启 Feign 对 Hystrix 的支持：

feign.hystrix.enabled=true

#### 1\. Fallback 方式

在 Feign 的客户端类上的 @FeignClient 注解中指定 fallback 进行回退（代码如下所示），创建一个 Feign 的客户端类 UserRemoteClient，为其配置 fallback。

```

@FeignClient (value = "eureka-client-user-service", fallback = UserRemoteClientFallback.class)
public interface UserRemoteClient {

    @GetMapping("/user/hello")
    String hello();
}
```

UserRemoteClientFallback 类需要实现 UserRemoteClient 类中所有的方法，返回回退时的内容，代码如下所示。

```

@Component
public class UserRemoteClientFallback implements UserRemoteClient {

    @Override
    public String hello() {
        return "fail";
    }
}

```

停掉所有 eureka-client-user-service 服务，然后访问 /callHello 接口，这个时候 eureka-client-user-service 服务是不可用的，必然会触发回退，返回的内容是 fail 字符串，这证明回退生效了。

在这种情况下，如果你的接口调用了多个服务的接口，那么只有 eureka-client-user-service 服务会没数据，不会影响别的服务，如果不用 Hystrix 回退处理，整个请求都将失败。

{
    code:200,
    message:"",
    data:{
        id:1,
        money:100.12,
        name:"fail"
    }
}

下面我们将启用 Hystrix 断路器禁用：

feign.hystrix.enabled=false

再次访问 /callHello 可以看到返回的就是 500 错误信息了，整个请求失败。

#### 2\. FallbackFactory 方式

通过 fallback 已经可以实现服务不可用时回退的功能，如果你想知道触发回退的原因，可以使用 FallbackFactory 来实现回退功能，代码如下所示。

```

@Component
public class UserRemoteClientFallbackFactory implements FallbackFactory<UserRemoteClient> {
    private Logger logger = LoggerFactory.getLogger(UserRemoteClientFallbackFactory.class);

    @Override
    public UserRemoteClient create(final Throwable cause) {
        logger.error("UserRemoteClient 回退：", cause);
        return new UserRemoteClient() {
            @Override
            public String hello() {
                return "fail";
            }
        };
    }
}
```

FallbackFactory 的使用就是在 @FeignClient 中用 fallbackFactory 指定回退处理类，代码如下所示。

@FeignClient(value = "eureka-client-user-service", configuration = FeignConfiguration.class, fallbackFactory = UserRemoteClientFallbackFactory.class)

笔者在这个回退处理的时候，将异常信息通过日志输出了，我们重新调用接口，可以看到异常信息在开发工具的控制台中输出了，FallbackFactory 和 Fallback 唯一的区别就在这里。

## Feign 中禁用 Hystrix

禁用 Hystrix 还是比较简单的，目前有两种方式可以禁用，一种是在属性文件中进行全部禁用，默认就是禁用的状态。

feign.hystrix.enabled=false

另一种是通过代码的方式禁用某个客户端，在 Feign 的配置类中增加如下所示的代码。

```

@Configuration
public class FeignConfiguration {
    @Bean 
    @Scope("prototype")
    public Feign.Builder feignBuilder() {
        return Feign.builder();
    }
}
```