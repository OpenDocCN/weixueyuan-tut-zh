# Zuul 中传递 Token 到路由的服务中

> 原文：[`c.biancheng.net/view/5508.html`](http://c.biancheng.net/view/5508.html)

服务之间接口调用的安全认证是通过 Feign 的请求拦截器统一在请求头中添加 Token 信息，实现认证调用。还有一种调用方式也是需要进行认证，就是我们的 API 网关转发到具体的服务，这时候就不能采用 Feign 拦截器的方式进行 Token 的传递。

在 Zuul 中我们可以用 pre 过滤器来做这件事情，在路由之前将 Token 信息添加到请求头中，然后将请求头转发到具体的服务上。

通过 Zuul 的 pre 过滤器进行 Token 的设置，代码如下所示。

```

/**
* 调用服务前添加认证请求头过滤器
**/
public class AuthHeaderFilter extends ZuulFilter {

    public AuthHeaderFilter() {
        super();
    }

    @Override
    public boolean shouldFilter() {
        RequestContext ctx = RequestContext.getCurrentContext();
        Object success = ctx.get("isSuccess");
        return success == null ? true : Boolean.parseBoolean(success.toString());
    }

    @Override
    public String filterType() {
        return "pre";
    }

    @Override
    public int filterOrder() {
        return 5;
    }

    @Override
    public Object run() {
        RequestContext ctx = RequestContext.getCurrentContext();
        ctx.addZuulRequestHeader("Authorization", System.getProperty("fangjia.auth.token"));
        return null;
    }
}
```

Token 的刷新机制和之前讲的一模一样，还是用那个定时器，直接复制过去即可，但是必须依赖申请 Token 的 Feign 客户端的定义。