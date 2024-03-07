# Apollo 高可用设计分析

> 原文：[`c.biancheng.net/view/5483.html`](http://c.biancheng.net/view/5483.html)

高可用是分布式系统架构设计中必须考虑的因素之一，它通常是指通过设计减少系统不能提供服务的时间。

Apollo 在高可用设计上下了很大的功夫，下面我们来简单的分析下：

#### 1）某台 Config Service 下线

无影响，Config Service 可用部署多个节点。

#### 2）所有 Config Service 下线

所有 Config Service 下线会影响客户端的使用，无法读取最新的配置。可采用读取本地缓存的配置文件来过渡。

#### 3）某台 Admin Service 下线

无影响，Admin Service 可用部署多个节点。

#### 4）所有 Admin Service 下线

Admin Service 是服务于 Portal，所有 Admin Service 下线之后只会影响 Portal 的操作，不会影响客户端，客户端是依赖 Config Service。

#### 5）某台 Portal 下线

Portal 可用部署多台，通过 Nginx 做负载，某台下线之后不影响使用。

#### 6）全部 Portal 下线

对客户端读取配置是没有影响的，只是不能通过 Portal 去查看，修改配置。

#### 7）数据库宕机

当配置的数据库宕机之后，对客户端是没有影响的，但是会导致 Portal 中无法更新配置。当客户端重启，这个时候如果需要重新拉取配置，就会有影响，可采取开启配置缓存的选项来避免数据库宕机带来的影响。

通过上面的分析，我们可以看出 Apollo 在可用性这块做得确实不错，各种场景会发生的问题都有备用方案，基本上不会有太大问题，大家放心大胆地使用吧。