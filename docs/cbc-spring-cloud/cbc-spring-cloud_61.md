# 服务消费方申请 Token

> 原文：[`c.biancheng.net/view/5504.html`](http://c.biancheng.net/view/5504.html)

目前服务提供方已经开启了调用认证，这意味着如果现在直接调用接口会被拦截，所以在调用之前需要进行认证，即获取 Token 并将其放到请求头中与请求头一起传输才可以调用接口。

#### 1\. 调用前获取 Token

获取 Token 前我们先定义一个 Feign 的客户端，代码如下所示。

```

/**
* 认证服务 API 调用客户端
**/
@FeignClient(value = "auth-service", path = "/oauth")
public interface AuthRemoteClient {
    /**
     * 调用认证, 获取 token
     *
     * @param query
     * @return
     */
    @PostMapping("/token")
    ResponseData auth(@RequestBody AuthQuery query);
}
```

通过 AuthRemoteClient 就可以获取 Token。

#### 2\. 缓存 Token 信息

如果每次调用接口之前都去认证一次，肯定是不行的，因为这样会导致性能降低，而且 Token 是可以设置过期时间的，完全没必要每次都去重新申请。

大家可以将 Token 缓存在本地或者 Redis 中。需要注意的是缓存时间必须小于 Token 的过期时间。

#### 3\. 采用定时器刷新 Token

就算获取的 Token 采用缓存来降低申请次数，这种方式也不是最优的方案。如果我们用的是 Feign 来消费接口，那么以下两种方式更好一些：一种方式就是在所有业务代码中调用接口前获取 Token，然后再进行相关设置。另一种是利用 Feign 提供的请求拦截器直接获取 Token，然后再进行相关设置。

采用定时器刷新 Token 是笔者认为最优的方案，其耦合程度很低，只需要添加一个定时任务即可。需要注意的是，定时的时间间隔必须小于 Token 的失效时间，如果 Token 是 24 小时过期，那么你可以 20 个小时定时刷新一次来保证调用的正确性。

定时刷新 Token 的代码如下所示。

```

/**
* 定时刷新 token
**/
@Component
public class TokenScheduledTask {
    private static Logger logger = LoggerFactory.getLogger(TokenScheduledTask.class);

    public final static long ONE_Minute = 60 * 1000 * 60 * 20;

    @Autowired
    private AuthRemoteClient authRemoteClient;

    /**
     * 刷新 Token
     */
    @Scheduled(fixedDelay = ONE_Minute)
    public void reloadApiToken() {
        String token = this.getToken();
        while (StringUtils.isBlank(token)) {
            try {
                Thread.sleep(1000);
                token = this.getToken();
            } catch (InterruptedException e) {
                logger.error("", e);
            }
        }
        System.setProperty("fangjia.auth.token", token);
    }

    public String getToken() {
        AuthQuery query = new AuthQuery();
        query.setAccessKey("1");
        query.setSecretKey("1");
        ResponseData response = authRemoteClient.auth(query);
        return response.getData() == null ? "" : response.getData().toString();
    }
}
```