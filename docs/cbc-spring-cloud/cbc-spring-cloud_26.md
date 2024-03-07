# Spring Cloud Hystrix 资源隔离策略（线程、信号量）

> 原文：[`c.biancheng.net/view/5389.html`](http://c.biancheng.net/view/5389.html)

Hystrix 的资源隔离策略有两种，分别为线程池和信号量。那我们为什么需要资源隔离呢？

在一个分布式系统中，服务之间都是相互调用的，例如，我们容器（Tomcat）配置的线程个数为 1000，服务 A-服务 R，其中服务 I 的并发量非常的大，需要 500 个线程来执行，此时，服务 I 又挂了，那么这 500 个线程很可能就夯死了，那么剩下的服务，总共可用的线程为 500 个，随着并发量的增大，剩余服务挂掉的风险就会越来越大，最后导致整个系统的所有服务都不可用，直到系统宕机。

以上就是服务的雪崩效应。Hystrix 就是用来做资源隔离的，比如说，当客户端向服务端发送请求时，给服务 I 分配了 10 个线程，只要超过了这个并发量就走降级服务，就算服务 I 挂了，最多也就导致服务 I 不可用，容器的 10 个线程不可用了，但是不会影响系统中的其他服务。

下面，我们就来具体说下这两种隔离策略。

## 信号量策略配置

用于隔离本地代码或可快速返回的远程调用可以直接使用信号量隔离，降低线程隔离的上下文切换开销。如 memcached，redis。

线程隔离会带来线程开销，有些场景（比如无网络请求场景）可能会因为用开销换隔离得不偿失，为此 hystrix 提供了信号量隔离。

主要适用于并发需求不大的依赖调用，因为如果并发需求较大，相应的信号量的数量就要设置得够大，因为 Tomcat 线程与处理线程为同一个线程，那么这个依赖调用就会占用过多的 Tomcat 线程资源，有可能会影响到其他服务的接收。

信号量策略配置方法代码如下所示。

```

super(HystrixCommand.Setter.withGroupKey(HystrixCommandGroupKey.Factory.asKey("MyGroup"))
        .andCommandPropertiesDefaults(HystrixCommandProperties.Setter()
            .withExecutionIsolationStrategy(HystrixCommandProperties.ExecutionIsolationStrategy.SEMAPHORE

            )));
    this.name = name;
}
```

之前在 run 方法中特意输出了线程名称，通过这个名称就可以确定当前是线程隔离还是信号量隔离。

## 线程隔离策略配置

执行依赖代码的线程与请求线程（比如 Tomcat 线程）分离，请求线程可以自由控制离开的时间，这也是我们通常说的异步编程，Hystrix 是结合 RxJava 来实现的异步编程。

通过为每个包裹了 HystrixCommand 的 API 接口设置独立的、固定大小的线程池（hystrix.threadpool.default.coreSize）来控制并发访问量，当线程饱和的时候可以拒绝服务，防止依赖问题扩散。

系统默认采用线程隔离策略，我们可以通过 andThreadPoolPropertiesDefaults 配置线程池的一些参数，代码如下所示。

```

public MyHystrixCommand(String name) {
    super(HystrixCommand.Setter.withGroupKey(HystrixCommandGroupKey.Factory.asKey("MyGroup"))
            .andCommandPropertiesDefaults(HystrixCommandProperties.Setter()
                    .withExecutionIsolationStrategy(HystrixCommandProperties.ExecutionIsolationStrategy.THREAD))
            .andThreadPoolPropertiesDefaults(HystrixThreadPoolProperties.Setter().withCoreSize(10)
                    .withMaxQueueSize(100).withMaximumSize(100)));
    this.name = name;
}
```

线程隔离策略的优点如下：

*   一个依赖调用可以给予一个线程池，这个依赖的异常不会影响其他的依赖。
*   使用线程可以完全隔离业务代码，请求线程可以快速返回。
*   可以完全模拟异步调用，方便异步编程。

线程隔离策略的缺点：使用线程池的缺点主要是增加了计算的开销。每一个依赖调用都会涉及到队列，调度，上下文切换，而这些操作都有可能在不同的线程中执行。