# spring-boot-starter-aop 及其使用场景说明

> 原文：[`c.biancheng.net/view/4648.html`](http://c.biancheng.net/view/4648.html)

如今，AOP（Aspect Oriented Programming）已经不是什么崭新的概念了，在经历了代码生成、动态代理、字节码增强甚至静态编译等不同时代的洗礼之后，Java 平台上的 AOP 方案基本上已经以 SpringAOP 结合 AspectJ 的方式稳固下来（虽然大家依然可以自己通过各种字节码工具偶尔“打造一些轮子”）。

现在 Spring 框架提供的 AOP 方案倡导了一种各取所长的方案，即使用 SpringAOP 的面向对象的方式来编写和组织织入逻辑，并使用 AspectJ 的 Pointcut 描述语言配合 Annotation 来标注和指明织入点（Jointpoint）。

原则上来说，我们只要引入 Spring 框架中 AOP 的相应依赖就可以直接使用 Spring 的 AOP 支持了，不过，为了进一步为大家使用 SpringAOP 提供便利，SpringBoot 还是“不厌其烦”地为我们提供了一个 spring-boot-starter-aop 自动配置模块。

spring-boot-starter-aop 自动配置行为由两部分内容组成：

1.  位于 spring-boot-autoconfigure 的 org.springframework.boot.autoconfigure.aop.AopAutoConfiguration 提供 @Configuration 配置类和相应的配置项。
2.  spring-boot-starter-aop 模块自身提供了针对 spring-aop、aspectjrt 和 aspectjweaver 的依赖。

一般情况下，只要项目依赖中加入了 spring-boot-starter-aop，其实就会自动触发 AOP 的关联行为，包括构建相应的 AutoProxyCreator，将横切关注点织入（Weave）相应的目标对象等，不过 AopAutoConfiguration 依然为我们提供了可怜的两个配置项，用来有限地干预 AOP 相关配置：

*   spring.aop.auto=true
*   spring.aop.proxy-target-class=false

对我们来说，这两个配置项的最大意义在于：允许我们投反对票，比如可以选择关闭自动的 aop 配置（spring.aop.auto=false），或者启用针对 class 而不是 interface 级别的 aop 代理（aop proxy）。

AOP 的应用场景很多，我们不妨以当下最热门的 APM（Application Performance Monitoring）为实例场景，尝试使用 spring-boot-starter-aop 的支持打造一个应用性能监控的工具原型。

## spring-boot-starter-aop 在构建 spring-boot-starter-metrics 自定义模块中的应用

对于应用性能监控来说，架构逻辑上其实很简单，基本上就是三步走（如图 1 所示）。

本节暂时只构建一个 spring-boot-starter-metrics 自定义的自动配置模块用来解决“应用性能数据采集”的问题。
![应用性能监控关键环节示意图](img/99bd0fad6fea31c2ca12b89b5ec906c9.png)
图 1  应用性能监控关键环节示意图
在此之前，有几个原则我们需要先说明一下：

虽然说采集应用性能数据可以帮助我们更好地分析和改进应用的性能指标，但这不意味着可以借着 APM 的名义对应用的核心职能形成侵害，加上应用性能数据采集功能一定会对应用的性能本身带来拖累，你拿到的所谓性能数据是分摊了你的数据采集方案带来的负担，所以，一般情况下，最好把应用性能数据采集模块的性能损耗控制在 10% 以内甚至更小。

SpringAOP 其实提供了多种横切逻辑织入机制（Weaving），性能损耗上也是各有差别，从运行期间的动态代理和字节码增强 Weavng，到类加载期间的 Weaving，甚至高冷的 AspectJ 二次静态编译 Weaving，大家可以根据情况灵活把握。

针对应用性能数据的采集，最好对应用开发者是透明的，通过配置外部化的形式，可以最大限度地剥离这部分对应用开发者来说非核心的关注点，只在部署和运行之前确定采集点并配置上线即可。

虽然本节实例采用基于 @Annotation 的方式来标注性能采集点，但不意味着这是最优的方式，更多是基于技术方案（SpringAOP）的现状给出的一种实践方式。

下面我们正式着手构建 spring-boot-starter-metrics 自定义的自动配置模块的设计和实现方案。

笔者一向是只在有必要的时候才重新“造轮子”，绝不会为了炫技而去“造轮子”，所以，本次的主角我们选择 Java 中的 Dropwizard Metrics 这个类库作为打造我们 APM 原型的起点。

Dropwizard Metrics 为我们提供了多种不同类型的应用数据度量方案，且通过相应的数据处理算法在性能和批量状态的管理上做了很优秀的工作，只不过，如果我们直接用它的 API 来对自己的应用代码进行度量的话，那写起来代码太多，而且这些性能代码混杂在应用的核心逻辑执行路径上，一个是界面不友好，另外一个就是不容易维护：

```

public class MockService implements InitializingBean {
    @Autowired
    MetricRegistry metricRegistry;
    private Timer timer;
    private Counter counter;

    // define more other metrics...
    public void doSth() {
        counter.inc();
        Timer.Context context = timer.time();
        try {
            System.out.println("just do something.");
        } finally {
            context.stop();
        }
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        timer = metricRegistry.timer("timerToProfilingDoSthMethod");
        counter = metricRegistry.counter("counterForDoSthMethod");

    }
}
```

所以，对于这些非功能性的性能度量代码，我们可以使用 AOP 的方式剥离到相应的 Aspect 中单独维护，而为了能够将这些性能度量的 Aspect 挂接到指定的待度量代码上，基于现有的方案选型。

可以使用 metrics-annotation 提供的一系列 Annotation 来标注织入位置，这样，开发者只要在需要度量的代码位置上标注相应的 Annotation，我们提供的 spring-boot-starter-metrics 自定义的自动配置模块就会自动地收集这些位置上指定的性能度量数据。

首先，我们通过 [`start.spring.io/`](http://start.spring.io/) 构建一个 SpringBoot 的脚手架项目，选择以 Maven 编译（选择用 Gradle 的同学自行甄别后面的配置如何具体进行），然后在创建好的 SpringBoot 脚手架项目的 pom.xml 中添加如下必要配置：

```

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
        http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.keevol</groupId>
    <artifactId>spring-boot-starter-metrics</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>
    <name>spring-boot-starter-metrics</name>
    <description>auto configuration module for dropwizard metrics</description>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.3.0.RELEASE</version>
        <relativePath /> <!-- lookup parent from repository -->
    </parent>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <java.version>1.8</java.version>
        <metrics.version>3.1.2</metrics.version>
    </properties> <!--其他配置 -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>io.dropwizard.metrics</groupId>
            <artifactId>metrics-core</artifactId>
            <version>
                ${metrics.version}
            </version>
        </dependency>
        <dependency>
            <groupId>io.dropwizard.metrics</groupId>
            <artifactId>metrics-annotation</artifactId>
            <version>${metrics.version}</version>
        </dependency>
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjrt</artifactId>
            <version>1.8.7</version>
        </dependency>
    </dependencies>
</project>
```

pom.xml 中有几个关键配置需要关注：

*   继承了 spring-boot-starter-parent，用于加入 springboot 的相关依赖。
*   添加了 spring-boot-starter-aop 依赖。
*   添加了 io.dropwizard.metrics 下相应的依赖，用来引入 dropwizard metrics 类库和必要的 Annotations。
*   添加了 spring-boot-starter-actuator，这个自动配置模块教程后面会跟大家进一步介绍，在这里我们主要是引入它对 dropwizard metrics 和 JMX 的一部分自动配置逻辑，比如针对 MetricRegistry 和 MBeanServer 的自动配置，这样我们就可以直接 @Autowired 来注入使用 MetricRegistry 和 MBeanServer。

至于 aspectjrt，是使用了最新的版本，原则上 spring-boot-starter-aop 已经有依赖，这里可以不用明确添加配置。

如果单单是一个提供必要依赖的自动配置模块，那么到这里其实就可以结束了，但我们的 spring-boot-starter-metrics 需要使用 AOP 提供相应的横切关注点逻辑。

所以，还需要编写并提供一些必要的代码组件，因此，最少我们先要提供一个 @Configuration 配置类，用于将我们即将提供的这些 AOP 逻辑暴露给使用者：

```

@Configuration
@ComponentScan({ "com.keevol.springboot.metrics.lifecycle",
        "com.keevol.springboot.metrics.aop" })
@AutoConfigureAfter(AopAutoConfiguration.class)
public class DropwizardMetricsMBeansAutoConfiguration {
    @Value("${metrics.mbeans.domain.name:com.keevol.metrics}")
    String metricsMBeansDomainName;
    @Autowired
    MBeanServer mbeanServer;
    @Autowired
    MetricRegistry metricRegistry;

    @Bean
    public JmxReporter jmxReporter() {
        JmxReporter reporter = JmxReporte.forRegistry(metricRegistry)
                .inDomain(metricsMBeansDomainName).registerWith(mbeanServer)
                .build();
        return reporter;
    }
}
```

然后就是将这个配置类添加到 META-INF/spring.factories：

org.springframework.boot.autoconfigure.EnableAutoConfiguration=\com.keevol.springboot.metrics.autocfg.DropwizardMetricsMBeansAuto-ConfigurationOK， 
不要认为将 spring-boot-starter-metrics 打包作为类库发布出去就可以了，AOP 相关的代码还没写。

我们回头来看 DropwizardMetricsMBeansAutoConfiguration 配置类，这个配置类的实现很简单，注入了 MBeanServer 和 MetricRegistry 的实例，并开放了一个 metrics.mbeans.domain.name 配置属性（默认值 com.keevol.metrics）便于使用者指定自定义的 MBean 暴露和访问的命名空间。

当然，以上给这些其实都不是重点，因为它们都只是为了将我们要采集的性能数据指标以 JMX 的形式暴露出去而服务的，重点在于 DropwizardMetricsMBeansAutoConfiguration 头顶上的那几顶“帽子”：

*   @Configuration 自然不必说了，这是一个 JavaConfig 配置类。
*   @ComponentScan（{"com.keevol.springboot.metrics.lifecycle"，"com.keevol.springboot.metrics.aop"}），为了简便，让 @ComponentScan 把这两个 java package 下的所有组件都加载到 IoC 容器中，这些组件就包括我们要提供的一系列与 AOP 和 Dropwizard Metrics 相关的实现逻辑。
*   @AutoConfigureAfter（AopAutoConfiguration.class）告诉 SpringBoot：“我希望 DropwizardMetricsMBeansAutoConfiguration 在 AopAutoConfiguration 完成之后进行配置”。

现在，最后的秘密就隐藏在 @ComponentScan 背后的两个 java package 之下了。

首先是 com.keevol.springboot.metrics.aop，在这个 java package 下面，我们只提供了一个 AutoMetricsAspect，其定义如下：

```

@Component
@Aspectpublic
class AutoMetricsAspect {
    protected ConcurrentMap<String, Meter> meters = new ConcurrentHashMap<>();
    protected ConcurrentMap<String, Meter> exceptionMeters = new ConcurrentHashMap<>();
    protected ConcurrentMap<String, Timer> timers = new ConcurrentHashMap<>();
    protected ConcurrentMap<String, Counter> counters = new ConcurrentHashMap<>();
    @Autowired
    MetricRegistry metricRegistry;

    @Pointcut(value = "execution(public * *(..))")
    public void publicMethods() {
    }

    @Before("publicMethods() && @annotation(countedAnnotation)")
    public void instrumentCounted(JoinPoint jp, Counted countedAnnotation) {
        String name = name(jp.getTarget().getClass(), StringUtils.hasLength(countedAnnotation.name()) ? countedAnnotation.name() : jp.getSignature().getName(), "counter");
        Counter counter = counters.computeIfAbsent(name, key -> metricRegistry.counter(key));
        counter.inc();
    }   

    @Before("publicMethods() && @annotation(meteredAnnotation)")
    public void instrumentMetered(JoinPoint jp, Metered meteredAnnotation) {
        String name = name(jp.getTarget().getClass(), StringUtils.hasLength(meteredAnnotation.name()) ? meteredAnnotation.name() : jp.getSignature().getName(), "meter");
        Meter meter = meters.computeIfAbsent(name, key -> metricRegistry.meter(key));
        meter.mark();
    }   
    @AfterThrowing(pointcut = "publicMethods() && @annotation(exMe-teredAnnotation)", throwing = "ex")
    public void instrumentExceptionMetered(JoinPoint jp, Throwable ex, ExceptionMetered exMeteredAnnotation) {
        String name = name(jp.getTarget().getClass(), StringUtils.hasLength(exMeteredAnnotation.name()) ? exMeteredAnnotation.name() : jp.getSignature().getName(), "meter", "exception");
        Meter meter = exceptionMeters.computeIfAbsent(name, meterName -> metricRegistry.meter(meterName));
        meter.mark();
    }   
    @Around("publicMethods() && @annotation(timedAnnotation)")
    public Object instrumentTimed(ProceedingJoinPoint pjp, Timed timedAnnotation) throws Throwable {
        String name = name(pjp.getTarget().getClass(), StringUtils.hasLength(timedAnnotation.name()) ? timedAnnotation.name() : pjp.getSignature().getName(), "timer");
        Timer timer = timers.computeIfAbsent(name, inputName -> metricRegistry.timer(inputName));
        Timer.Context tc = timer.time();
        try {
            return pjp.proceed();
        } finally {
            tc.stop();
        }
    }
}
```

@Aspect+@Component 的目的在于告诉 Spring 框架：“我是一个 AOP 的 Aspect 实现类并且你可以通过 @ComponentScan 把我加入 IoC 容器之中。”当然，这不是重点。

io.dropwizard.metrics：metrics-annotation 这个依赖包为我们提供了几个有趣的 Annotation：

*   Timed
*   Gauge
*   Counted
*   Metered
*   ExceptionMetered

这些语义良好的 Annotation 定义可以用来标注相应的 AOP 逻辑扩展点，比如，针对同一个 MockService，我们可以将性能数据的度量和采集简化为只标注一两个 Annotation 就可以了：

```

@Component
public class MockService {
    @Timed
    @Counted
    public void doSth() {
        System.out.println("just do something.");
    }
} 
```

但是，Annotation 注定只是 Annotation，它们只是一些标记信息，要让它们发挥作用，需要有“伯乐”的眷顾，所以，AutoMetricsAspect 在这里就是这些 Dropwizard Metrics Annotation 的“伯乐”。

通过拦截每一个 public 方法并检查方法上是否存在某个 metrics annotation，我们就可以根据具体的 metrics annotation 的类型，为匹配的方法注入相应性能数据采集代码逻辑，从而完成整个基于 AOP 和 dropwizard metrics 的应用性能数据采集方案的实现。

受限于 SpringAOP 自身的一些限制，并不是所有 AOP 的 Joinpoint 类型都支持，而且，以上原型代码方向也不见得是性能最优的方案，大家需要结合自己的目标和手上可用的技术手段，根据自己的具体应用场景具体分析和权衡。

至此，整个基于 spring-boot-starter-aop 的 spring-boot-starter-metrics 自定义自动配置模块宣告完工，对于想了解更多细节，或者想寻找直接可用方案的读者，可以参考开源项目 [`github.com/ryantenney/metrics-spring`](https://github.com/ryantenney/metrics-spring)。