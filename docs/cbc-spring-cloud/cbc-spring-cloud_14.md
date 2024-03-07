# Spring Cloud 使用 Eureka 集群搭建实现高可用服务注册中心

> 原文：[`c.biancheng.net/view/5332.html`](http://c.biancheng.net/view/5332.html)

前面我们搭建的注册中心只适合本地开发使用，在生产环境中必须搭建一个集群来保证高可用。Eureka 的集群搭建方法很简单：每一台 Eureka 只需要在配置中指定另外多个 Eureka 的地址就可以实现一个集群的搭建了。

下面我们以 2 个节点为例来说明搭建方式。假设我们有 master 和 slaveone 两台机器，需要做的就是：

*   将 master 注册到 slaveone 上面。
*   将 slaveone 注册到 master 上面。

如果是 3 台机器，以此类推：

*   将 master 注册到 slaveone 和 slavetwo 上面。
*   将 slaveone 注册到 master 和 slavetwo 上面。
*   将 slavetwo 注册到 master 和 slaveone 上面。

## 搭建步骤

创建一个新的项目 eureka-server-cluster，配置跟 eureka-server 一样。

首先，我们需要增加 2 个属性文件，在不同的环境下启动不同的实例。增加 application-master.properties：

server.port=8761
# 指向你的从节点的 Eureka
eureka.client.serviceUrl.defaultZone=http://用户名:密码@localhost:8762/eureka/

增加 application-slaveone.properties：

server.port=8762
# 指向你的主节点的 Eureka
eureka.client.serviceUrl.defaultZone=http://用户名:密码 @localhost:8761/eureka/

在 application.properties 中添加下面的内容：

spring.application.name=eureka-server-cluster
# 由于该应用为注册中心, 所以设置为 false, 代表不向注册中心注册自己
eureka.client.register-with-eureka=false
# 由于注册中心的职责就是维护服务实例, 并不需要检索服务, 所以也设置为 false
eureka.client.fetch-registry=false

spring.security.user.name=zhangsan
spring.security.user.password=123456

# 指定不同的环境
spring.profiles.active=master

在 A 机器上默认用 master 启动，然后在 B 机器上加上 --spring.profiles.active=slaveone 启动即可。

这样就将 master 注册到了 slaveone 中，将 slaveone 注册到了 master 中，无论谁出现问题，应用都能继续使用存活的注册中心。

之前在客户端中我们通过配置 eureka.client.serviceUrl.defaultZone 来指定对应的注册中心，当我们的注册中心有多个节点后，就需要修改 eureka.client.serviceUrl.defaultZone 的配置为多个节点的地址，多个地址用英文逗号隔开即可：

eureka.client.serviceUrl.defaultZone=http://zhangsan:123456@localhost:8761
                /eureka/,http://zhangsan:123456@localhost:8762/eureka/