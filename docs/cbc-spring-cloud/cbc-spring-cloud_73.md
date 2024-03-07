# 微服务架构下如何获取用户信息并认证？

> 原文：[`c.biancheng.net/view/5539.html`](http://c.biancheng.net/view/5539.html)

在传统的单体项目中，我们对用户的认证通常就在项目里面，当拆分成微服务之后，一个业务操作会涉及多个服务。那么怎么对用户做认证？服务中又是如何获取用户信息的？这些操作都可以在 API 网关中实现。

## 动态管理不需要拦截的 API 请求

并不是所有的 API 都需要认证，比如登录接口。我们需要一个能够动态添加 API 白名单的功能，凡是在这个白名单当中的，我们就不做认证。这个配置信息需要能够实时生效，这就用上了我们的配置管理 Apollo。

在 API 网关中创建一个 Apollo 的配置类，代码如下所示。

```

@Data
@Configuration
public class BasicConf {

    // API 接口白名单, 多个用逗号分隔
    @Value("${apiWhiteStr:/zuul-extend-user-service/user/login}")
    private String apiWhiteStr;
}
```

编写认证的 Filter，代码如下所示。

```

/**
* 认证过滤器
**/
public class AuthFilter extends ZuulFilter {

    @Autowired
    private BasicConf basicConf;

    public AuthFilter() {
        super();
    }

    @Override
    public boolean shouldFilter() {
        return true;
    }

    @Override
    public String filterType() {
        return "pre";
    }

    @Override
    public int filterOrder() {
        return 1;
    }

    @Override
    public Object run() {
        RequestContext ctx = RequestContext.getCurrentContext();
        String apis = basicConf.getApiWhiteStr();
        // 白名单，放过
        List<String> whileApis = Arrays.asList(apis.split(","));
        String uri = ctx.getRequest().getRequestURI();
        if (whileApis.contains(uri)) {
            return null;
        }
        // path uri 处 理
        for (String wapi : whileApis) {
            if (wapi.contains("{"} && wapi.contains(")")) {
                if (wapi.split("/").length == uri.split("/").length) {
                    String reg = wapi.replaceAll("\\{.*}", ".*{1,}");
                    Pattern r = Pattern.compile(reg);
                    Matcher m = r.matcher(uri);
                    if (m.find()) {
                        return null;
                    }
                }
            }
        }
        return null;
    }
}
```

在 Filter 中注入我们的 BasicConf 配置，在 run 方法里面执行判断的逻辑，将配置的白名单信息转成 List，然后判断当前请求的 URI 是否在白名单中，存在则放过。

下面还有一段是 Path URI 的处理，这是解决 /user/{userId} 这种类型的 URI，URI 中有动态的参数，直接匹配是否相等肯定是不行的。

最后配置 Filter 即可启用，代码如下所示。

@Bean
public AuthFilter authFilter() {
    return new AuthFilter();
}

当有不需要认证的接口时，直接在 Apollo 后台修改一下配置信息即可实时生效。

## 创建认证的用户服务

用户服务是每个产品必备的一个服务，可以管理这个产品的用户信息。我们用到的用户服务只是演示认证，所以只提供一个登录的接口即可。

登录接口代码如下所示。

```

/**
* 用户登录
*
* @param query
* @return
*/
@ApiOperation(value = "用户登录", notes = "企业用户认证接口，参数为必填项")
@PostMapping("/login")
public ResponseData login(@ApiParam(value = "登录参数", required = true) @RequestBody LoginQuery query) {
    if (query == null || query.getEid() == null || StringUtils.isBlank(query.getUid())) {
        return ResponseData.failByParam("eid 和 uid 不能为空");
    }
    return ResponseData.ok(enterpriseProductUserService.login(query.getEid(), query.getUid()));
}
```

Service 中的 login 方法用来判断是否成功登录，成功则用 JWT 将用户 ID 加密返回一个 Token。此处只是为了模拟，真实环境中需要去查数据库，代码如下所示。

```

public String login(Long eid, String uid) {
    JWTUtils jwtUtils = JWTUtils.getInstance();
    if (eid.equals(1L) && uid.equals("1001")) {
        return jwtUtils.getToken(uid);
    }
    return null;
}
```

## 路由之前的认证

除了我们之前讲解的，一些 API 由于特殊的需求，不需要做认证，我们可以用配置的方式来放行，其余的都需要认证，只有合法登录后的用户才能调用。当用户调用用户服务中的登录接口，登录成功之后就能拿到 Token，在请求其他的接口时带上 Token，就可以在 Zuul 的 Filter 中对这个 Token 进行认证。

验证逻辑和之前的 API 白名单是在一个 Filter 中进行的，在 path uri 处理之后进行认证，代码如下所示。

```

// 验证 TOKEN
if (!StringUtils.hasText(token)) {
    ctx.setSendZuulResponse(false);
    ctx.set("isSuccess", false);
    ResponseData data = ResponseData.fail("非法请求【缺少 Authorization 信息】", ResponseCode.NO_AUTH_CODE.getCode());
    ctx.setResponseBody(JsonUtils.toJson(data));
    ctx.getResponse().setContentType("application/json; charset=utf-8");
    return null;
}

JWTUtils.JWTResult jwt = jwtUtils.checkToken(token);
if (!jwt.isStatus()) {
    ctx.setSendZuulResponse(false);
    ctx.set("isSuccess", false);
    ResponseData data = ResponseData.fail(jwt.getMsg(), jwt.getCode());
    ctx.setResponseBody(JsonUtils.toJson(data));
    ctx.getResponse().setContentType("application/json; charset=utf-8");
    return null;
}
ctx.addZuulRequestHeader("uid", jwt.getUid());
```

从请求头中获取 Token，如果没有就拦截并给出友好提示，设置 isSuccess=false 告诉下面的 Filter 不需要执行了。有 Token 则验证 Token 的合法性，合法则放行，不合法就拦截并给出友好提示。

## 向下游微服务中传递认证之后的用户信息

传统的单体项目中我们通常都是使用 Session 来存储登录后的用户信息，但这样会导致做了集群后的用户信息有问题，在 A 服务上登录了，下次被转发到 B 服务区，又得重新登录一次。为了解决这个问题，通常采用 Session 共享的方式来解决，比如 Spring Session 这种框架。

在微服务下如何解决这个问题呢？为了提高并发性能，方便快速扩容，服务都被设计成了无状态的，不需要对每个服务都进行用户是否登录的判断，只需要统一在 API 网关中认证好即可。

在 API 网关中认证之后如何把用户信息传递给下方的服务就是我们需要关注的了，在 Zuul 中可以将认证之后的用户信息通过请求头的方式传递给下方服务，比如如下代码所示的方式。

ctx.addZuulRequestHeader("uid", jwt.getUid());

在具体的服务中就可以通过 request 对象来获取传递过来的用户信息，代码如下所示。

```

@GetMapping("/article/callHello")
public String callHello() {
    System.err.println("用户 ID:" + request.getHeader("uid"));
    return userRemoteClient.hello();
}
```

## 内部服务间的用户信息传递

关于用户信息的传递问题，我们知道从 API 网关过来的请求，经过认证之后是可以拿到认证后的用户 ID，这时候我们可以通过 addZuulRequestHeader 的方式将用户 ID 传递到我们转发的服务上去，但如果从网关转发到 A 服务，A 服务需要调用 B 服务的接口，那么我想在 B 服务中也能通过 request.getHeader("uid") 去获取用户 ID，这个时候该怎么处理？

关于这种需求，我的建议是直接通过网关转发过去的接口。我们可以通过 request.getHeader("uid") 来获取网关带过来的用户 ID，然后服务之前调用的话可以通过参数的方式告诉被调用的服务，A 服务调用 B 服务的 hello 接口，那么 hello 接口中增加一个 uid 的参数即可，此时的用户 ID 是网关给我们的，已经是认证过的了，可以直接使用。

如果想做成类似于 Session 共享的方式也可以，那么当 A 服务调用 B 服务时，你就得通过在框架层面将用户 ID 传递到 B 服务当中，但是这个不能让每个开发人员去关心，必须封装成统一的处理。

我们可以这样做，首先我们的场景是 API 网关中会通过请求头将用户 ID 传递到转发的服务中，那么我们可以通过过滤器来获取这个值，然后进行传递操作，代码如下所示。

```

public class HttpHeaderParamFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        HttpServletRequest httpRequest = (HttpServletRequest) request;
        HttpServletResponse httpResponse = (HttpServletResponse) response;
        httpResponse.setCharacterEncoding("UTF-8");
        httpResponse.setContentType("application/json; charset=utf-8");
        String uid = httpRequest.getHeader("uid");
        RibbonFilterContextHolder.getCurrentContext().add("uid", uid);
        chain.doFilter(httpRequest, response);
    }

    @Override
    public void destroy() {
    }
}
```

RibbonFilterContextHolder 是通过 InheritableThreadLocal 在线程之间进行数据传递的。这步走完后请求就转发到了我们具体的接口上面，然后这个接口中就会用 Feign 去调用 B 服务的接口，所以接下来需要用 Feign 的拦截器将刚刚获取的用户 ID 重新传递到 B 服务中，代码如下所示。

```

public class FeignBasicAuthRequestInterceptor implements RequestInterceptor {
    public FeignBasicAuthRequestInterceptor() {

    }

    @Override
    public void apply(RequestTemplate template) {
        Map<String, String> attributes = RibbonFilterContextHolder.getCurrentContext().getAttributes();
        for (String key : attributes.keySet()) {
            String value = attributes.get(key);
            template.header(key, value);
        }
    }
}
```

通过获取 InheritableThreadLocal 中的数据添加到请求头中，这里不用具体的名字去获取数据是为了扩展，这样后面添加任何的参数都能直接传递过去了。

Feign 的拦截器使用需要在 @FeignClient 注解中指定 Feign 的自定义配置，自定义配置类中配置 Feign 的拦截器即可。

拦截器只需要注册下就可以使用了，本套方案不用改变当前任何业务代码，代码如下所示。

```

public class FeignBasicAuthRequestInterceptor implements RequestInterceptor {
    @Bean
    public FilterRegistrationBean filterRegistrationBean() {
        FilterRegistrationBean registrationBean = new FilterRegistrationBean();
        HttpHeaderParamFilter httpHeaderParamFilter = new HttpHeaderParamFilter();
        registrationBean.setFilter(httpHeaderParamFilter);
        List<String> urlPatterns = new ArrayList<String>(1);
        urlPatterns.add("/*");
        registrationBean.setUrlPatterns(urlPatterns);
        return registrationBean;
    }
}
```