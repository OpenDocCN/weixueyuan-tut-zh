# Spring Cloud Ribbon 自定义负载均衡策略

> 原文：[`c.biancheng.net/view/5354.html`](http://c.biancheng.net/view/5354.html)

通过实现 IRule 接口可以自定义负载策略，主要的选择服务逻辑在 choose 方法中。我们这边只是演示怎么自定义负载策略，所以没写选择的逻辑，直接返回服务列表中第一个服务。具体代码如下所示。

```

public class MyRule implements IRule {

    private ILoadBalancer lb;

    @Override
    public Server choose(Object key) {
        List<Server> servers = lb.getAllServers();
        for (Server server : servers) {
            System.out.println(server.getHostPort());
        }
        return servers.get(0);
    }

    @Override
    public void setLoadBalancer(ILoadBalancer lb) {
        this.lb = lb;
    }

    @Override
    public ILoadBalancer getLoadBalancer() {
        return lb;
    }
}
```

在 Spring Cloud 中，可通过配置的方式使用自定义的负载策略，ribbon-config-demo 是调用的服务名称。

ribbon-config-demo.ribbon.NFLoadBalancerRuleClassName=net.biancheng.ribbon_eureka_demo.rule.MyRule

重启服务，访问调用了其他服务的接口，可以看到控制台的输出信息中已经有了我们自定义策略中输出的服务信息，并且每次都是调用第一个服务。这跟我们的逻辑是相匹配的。