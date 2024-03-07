# Spring Cloud Eureka 是什么？

> 原文：[`c.biancheng.net/view/5320.html`](http://c.biancheng.net/view/5320.html)

Spring Cloud Eureka 是 Spring Cloud Netflix 微服务套件的一部分，基于 Netflix Eureka 做了二次封装，主要负责实现微服务架构中的服务治理功能。

Spring Cloud Eureka 是一个基于 REST 的服务，并且提供了基于 Java 的客户端组件，能够非常方便地将服务注册到 Spring Cloud Eureka 中进行统一管理。

服务治理是微服务架构中必不可少的一部分，阿里开源的 Dubbo 框架就是针对服务治理的。服务治理必须要有一个注册中心，除了用 Eureka 作为注册中心外，我们还可以使用 Consul、Etcd、Zookeeper 等来作为服务的注册中心。

用过 Dubbo 的读者应该清楚，Dubbo 中也有几种注册中心，比如基于 Zookeeper、基于 Redis 等，不过用得最多的还是 Zookeeper 方式。

至于使用哪种方式都是可以的，注册中心无非就是管理所有服务的信息和状态。若用我们生活中的例子来说明的话，笔者觉得 12306 网站比较合适。

首先，12306 网站就好比一个注册中心，顾客就好比调用的客户端，当他们需要坐火车时，就会登录 12306 网站上查询余票，有票就可以购买，然后获取火车的车次、时间等，最后出发。

程序也是一样，当你需要调用某一个服务的时候，你会先去 Eureka 中去拉取服务列表，查看你调用的服务在不在其中，在的话就拿到服务地址、端口等信息，然后调用。

注册中心带来的好处就是，不需要知道有多少提供方，你只需要关注注册中心即可，就像顾客不必关心有多少火车在开行，只需要去 12306 网站上看有没有票就可以了。

为什么 Eureka 比 Zookeeper 更适合作为注册中心呢？主要是因为 Eureka 是基于 AP 原则构建的，而 ZooKeeper 是基于 CP 原则构建的。

在分布式系统领域有个著名的 CAP 定理，即 C 为数据一致性；A 为服务可用性；P 为服务对网络分区故障的容错性。这三个特性在任何分布式系统中都不能同时满足，最多同时满足两个。

Zookeeper 有一个 Leader，而且在这个 Leader 无法使用的时候通过 Paxos（ZAB）算法选举出一个新的 Leader。这个 Leader 的任务就是保证写数据的时候只向这个 Leader 写入，Leader 会同步信息到其他节点。通过这个操作就可以保证数据的一致性。

总而言之，想要保证 AP 就要用 Eureka，想要保证 CP 就要用 Zookeeper。

Dubbo 中大部分都是基于 Zookeeper 作为注册中心的。Spring Cloud 中当然首选 Eureka。