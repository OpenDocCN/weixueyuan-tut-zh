# Eureka 开发时快速移除失效服务

> 原文：[`c.biancheng.net/view/5343.html`](http://c.biancheng.net/view/5343.html)

在实际开发过程中，我们可能会不停地重启服务，由于 Eureka 有自己的保护机制，故节点下线后，服务信息还会一直存在于 Eureka 中。我们可以通过增加一些配置让移除的速度更快一点，当然只在开发环境下使用，生产环境下不推荐使用。

首先在我们的 eureka-server 中增加两个配置，分别是关闭自我保护和清理间隔：

eureka.server.enable-self-preservation=false
# 默认 60000 毫秒
eureka.server.eviction-interval-timer-in-ms=5000

然后在具体的客户端服务中配置下面的内容：

eureka.client.healthcheck.enabled=true
# 默认 30 秒
eureka.instance.lease-renewal-interval-in-seconds=5
# 默认 90 秒
eureka.instance.lease-expiration-duration-in-seconds=5

eureka.client.healthcheck.enabled 用于开启健康检查，需要在 pom.xml 中引入 actuator 的依赖，代码如下所示。

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

其中：

*   eureka.instance.lease-renewal-interval-in-seconds 表示 Eureka Client 发送心跳给 server 端的频率。
*   eureka.instance.lease-expiration-duration-in-seconds 表示 Eureka Server 至上一次收到 client 的心跳之后，等待下一次心跳的超时时间，在这个时间内若没收到下一次心跳，则移除该 Instance。

更多的 Instance 配置信息可参考源码中的配置类：org.springframework.cloud.netflix.eureka.EurekaInstanceConfigBean。

更多的 Server 配置信息可参考源码中的配置类：org.springframework.cloud.netflix.eureka.server.EurekaServerConfigBean。