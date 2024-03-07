# Spring Boot 的自动配置

> 原文：[`c.biancheng.net/view/4637.html`](http://c.biancheng.net/view/4637.html)

在教程《@SpringBootApplication 注解》中讲到 @EnableAutoConfiguration 可以借助 SpringFactoriesLoader 这个特性将标注了 @Configuration 的 JavaConfig 类“一股脑儿”的汇总并加载到最终的 ApplicationContext，不过，这其实只是“简化版”的说明。

实际上，基于 @EnableAutoConfiguration 的自动配置功能拥有更加强大的调控能力，通过配合比如基于条件的配置能力或者调整加载顺序，我们可以对自动配置进行更加细粒度的调整和控制。

## 基于条件的自动配置

基于条件的自动配置来源于 Spring 框架中“基于条件的配置”这一特性。在 Spring 框架中，我们可以使用 @Conditional 这个 Annotation 配合 @Configuration 或者 @Bean 等 Annotation 来干预一个配置或者 bean 定义是否能够生效，其最终实现的效果或者语义类似于如下伪代码：

if(符合 @Conditional 规定的条件){
    加载当前配置(enable current Configuration)或者注册当前 bean 定义；
}

要实现基于条件的配置，我们只要通过 @Conditional 指定自己的 Condition 实现类就可以了（可以应用于类型 Type 的标注或者方法 Method 的标注）：

@Conditional({MyCondition1.class, MyCondition2.class, ...})

最主要的是，@Conditional 可以作为一个 Meta Annotation 用来标注其他 Annotation 实现类，从而构建各色的复合 Annotation，比如 SpringBoot 的 autoconfigure 模块就基于这一优良的革命传统，实现了一批 Annotation（位于 org.springframework.boot.autoconfigure.condition 包下），条件注解如下：

*   @ConditionalOnBean：当容器里有指定的 Bean 的条件下。
*   @ConditionalOnClass：当类路径下有指定的类的条件下。
*   @ConditionalOnExpression：基于 SpEL 表达式作为判断条件。
*   @ConditionalOnJava：基于 JVM 版本作为判断条件。
*   @ConditionalOnJndi：在 JNDI 存在的条件下查找指定的位置。
*   @ConditionalOnMissingBean：当容器里没有指定 Bean 的情况下。
*   @ConditionalOnMissingClass：当类路径下没有指定的类的条件下。
*   @ConditionalOnNotWebApplication：当前项目不是 Web 项目的条件下。
*   @ConditionalOnProperty：指定的属性是否有指定的值。
*   @ConditionalOnResource：类路径是否有指定的值。
*   @ConditionalOnSingleCandidate：当指定 Bean 在容器中只有一个，或者虽然有多个但是指定首选的 Bean。
*   @ConditionalOnWebApplication：当前项目是 Web 项目的条件下。

有了这些复合 Annotation 的配合，我们就可以结合 @EnableAuto-Configurationn 实现基于条件的自动配置了。

SpringBoot 能够风靡，很大一部分功劳需要归功于它预先提供的一系列自动配置的依赖模块，而这些依赖模块都是基于以上 @Conditional 复合 Annotation 实现的，这也意味着所有的这些依赖模块都是按需加载的，只有符合某些特定条件，这些依赖模块才会生效，这也就是我们所谓的“智能”自动配置。

## 调整自动配置的顺序

在实现自动配置的过程中，除了可以提供基于条件的配置，我们还可以对当前要提供的配置或者组件的加载顺序进行相应调整，从而让这些配置或者组件之间的依赖分析和组装可以顺利完成。

我们可以使用 @org.springframework.boot.autoconfigure.AutoConfigureBefore 或者 @org.springframework.boot.autoconfigure.AutoConfigureAfter 让当前配置或者组件在某个其他组件之前或者之后进行，比如，假设我们希望某些 JMX 操作相关的 bean 定义在 MBeanServer 配置完成之后进行，那么我们就可以提供一个类似如下的配置：

```

@Configuration
@AutoConfigureAfter(JmxAutoConfiguration.class)
public class AfterMBeanServerReadyConfiguration {
    @AutoWired
    MBeanServer mBeanServer;

    //通过 @Bean 添加必要的 bean 定义
}
```

至此，我们对 SpringBoot 的核心组件完成了基本的剖析，综合来看，大部分的东西都是 Spring 框架背后原有的一些概念和实践方式，SpringBoot 只是在这些概念和实践方式上对特定的场景实现进行了固化和升华，而也恰恰是这些固化让我们开发基于 Spring 框架的应用更加方便高效。