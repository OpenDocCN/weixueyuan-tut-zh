# Spring Cloud 使用 Turbine 实现集群监控

> 原文：[`c.biancheng.net/view/5379.html`](http://c.biancheng.net/view/5379.html)

前面我们实现了对单个服务实例的监控，当然在实际应用中，单个实例的监控数据没有多大的价值，我们更需要的是一个集群系统的监控信息，这时我们就需要 Turbine。

Turbine 是用来监控集群的，通过它来汇集监控信息，并将聚合后的信息提供给 Hystrix Dashboard 来集中展示和监控。

## Turbine 使用

Turbine 是聚合服务器发送事件流数据的一个工具。Hystrix 只能监控单个节点，然后通过 dashboard 进行展示。实际生产中都为集群，这个时候我们可以通过 Turbine 来监控集群下 Hystrix 的 metrics 情况，通过 Eureka 来发现 Hystrix 服务。

本节在介绍 Turbine 的用法时就不再单独创建一个新项目了，在之前的 hystrix-dashboard-demo 中进行修改来支持 Turbine 即可。

首先增加 Turbine 的依赖，代码如下所示。

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-turbine</artifactId>
</dependency>

在启动类上增加 @EnableTurbine 和 @EnableDiscoveryClient。在属性文件中配置如下内容：

eureka.client.serviceUrl.defaultZone=http://zhangsan:123456@localhost:8761/eureka/
turbine.appConfig=hystrix-feign-demo
turbine.aggregator.clusterConfig=default
turbine.clusterNameExpression=new String("default")

其中：

*   turbine.appConfig：配置需要聚合的服务名称。
*   turbine.aggregator.clusterConfig：Turbine 需要聚合的集群名称。
*   turbine.clusterNameExpression：集群名表达式。

这里用默认的集群名称 default。

重启服务，就可以使用 http://localhost:9011/turbine.stream 来访问集群的监控数据了。Turbine 会通过在 Eureka 中查找服务的 homePageUrl 加上 hystrix.stream 来获取其他服务的监控数据，并将其汇总显示。

## context-path 导致监控失败

如果被监控的服务中设置了 context-path，则会导致 Turbine 无法获取监控数据，如图 1 所示。

![dashboard 数据监控失败页面](img/8ab45a90012624d4f1812c4cd1e7481d.png)
图 1  dashboard 数据监控失败页面
这个时候需要在 Turbine 中指定 turbine.instanceUrlSuffix 来解决这个问题：

turbine.instanceUrlSuffix=/sub/hystrix.stream

sub 用于监控服务的 context-path。上面这种方式是全局配置，会有一个问题，就是一般我们在使用中会用一个集群去监控多个服务，如果每个服务的 context-path 都不一样，这个时候有一些就会出问题，那么就需要对每个服务做一个集群，然后配置集群对应的 context-path：

turbine.instanceUrlSuffix. 集群名称 =/sub/hystrix.stream