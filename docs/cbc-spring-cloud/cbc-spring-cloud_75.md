# 服务降级是什么？Spring Cloud 如何实现？

> 原文：[`c.biancheng.net/view/5550.html`](http://c.biancheng.net/view/5550.html)

当访问量剧增，服务出现问题时，需要做一些处理，比如服务降级。服务降级就是将某些服务停掉或者不进行业务处理，释放资源来维持主要服务的功能。

某电商网站在搞活动时，活动期间压力太大，如果再进行下去，整个系统有可能挂掉，这个时候可以释放掉一些资源，将一些不那么重要的服务采取降级措施，比如登录、注册。登录服务停掉之后就不会有更多的用户抢购，同时释放了一些资源，登录、注册服务就算停掉了也不影响商品抢购。

服务降级有很多种方式，最好的方式就是利用 Docker 来实现。当需要对某个服务进行降级时，直接将这个服务所有的容器停掉，需要恢复的时候重新启动就可以了。

还有就是在 API 网关层进行处理，当某个服务被降级了，前端过来的请求就直接拒绝掉，不往内部服务转发，将流量挡回去。

在 Zuul 中对服务进行动态降级，结合我们的配置中心来做。

定义 Apollo 配置类，存储需要降级的服务信息见如下代码。

```

@Data
@Configuration
public class BasicConf {
    // 降级的服务 ID，多个用逗号分隔
    @Value("${downGradeServiceStr:default}")
    private String downGradeServiceStr;
}
```

编写过滤器来执行降级逻辑，见如下代码。

```

public class DownGradeFilter extends ZuulFilter {

    @Autowired
    private BasicConf basicConf;

    public DownGradeFilter() {
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
        return "route";
    }

    @Override
    public int filterOrder() {
        return 4;
    }

    @Override
    public Object run() {
        RequestContext ctx = RequestContext.getCurrentContext();
        Object serviceId = ctx.get("serviceId");
        if (serviceId != null && basicConf != null) {
            List<String> serviceIds = Arrays.asList(basicConf.getDownGradeServiceStr().split(","));
            if (serviceIds.contains(serviceId.toString())) {
                ctx.setSendZuulResponse(false);
                ctx.set("isSuccess", false);
                ResponseData data = ResponseData.fail("服务降级中", ResponseCode.DOWNGRADE.getCode());
                ctx.setResponseBody(JsonUtils.toJson(data));
                ctx.getResponse().setContentType("application/json; charset=utf-8");
                return null;
            }
        }
        return null;
    }
}
```

主要逻辑在 run 方法中，通过 RequestContext 获取即将路由的服务 ID，通过配置信息获取降级的服务信息，如果当前路由的服务在其中，就直接拒绝，返回对应的信息让客户端做对应的处理。

当需要降级的时候，直接在 Apollo 的后台改一下配置就可以马上生效，当然也可以做成自动的，比如监控某些指标，流量、负载等，当达到某些指标后就自动触发降级。