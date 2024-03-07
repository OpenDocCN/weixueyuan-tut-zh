# SpringBoot 是什么？SpringBoot 的优缺点有哪些？

> 原文：[`c.biancheng.net/view/4630.html`](http://c.biancheng.net/view/4630.html)

随着动态语言的流行（Ruby、Groovy、Scala、Node.js），Java 的开发显得格外的笨重，繁多的配置、低下的开发效率、复杂的部署流程以及第三方技术集成难度大。

在上述环境下，Spring Boot 应运而生。它使用“习惯优于配置”（项目中存在大量的配置，此外还内置一个习惯性的配置，让你无须手动进行配置）的理念让你的项目快速运行起来。

使用 Spring Boot 很容易创建一个独立运行（运行 jar，内嵌 Servlet 容器）、准生产级别的基于 Spring 框架的项目，使用 Spring Boot 你可以不用或者只需要很少的 Spring 配置。

## Spring Boot 核心功能

#### 1）独立运行的 Spring 项目

Spring Boot 可以以 jar 包的形式独立运行，运行一个 Spring Boot 项目只需通过 java–jar xx.jar 来运行。

#### 2）内嵌 Servlet 容器

Spring Boot 可选择内嵌 Tomcat、Jetty 或者 Undertow，这样我们无须以 war 包形式部署项目。

#### 3）提供 starter 简化 Maven 配置

Spring 提供了一系列的 starter pom 来简化 Maven 的依赖加载，例如，当你使用了 spring-boot-starter-web 时，会自动加入如图 1 所示的依赖包。

#### 4）自动配置 Spring

Spring Boot 会根据在类路径中的 jar 包、类，为 jar 包里的类自动配置 Bean，这样会极大地减少我们要使用的配置。当然，Spring Boot 只是考虑了大多数的开发场景，并不是所有的场景，若在实际开发中我们需要自动配置 Bean，而 Spring Boot 没有提供支持，则可以自定义自动配置。

#### 5）准生产的应用监控

Spring Boot 提供基于 http、ssh、telnet 对运行时的项目进行监控。

#### 6）无代码生成和 xml 配置

Spring Boot 的神奇的不是借助于代码生成来实现的，而是通过条件注解来实现的，这是 Spring 4.x 提供的新特性。Spring 4.x 提倡使用 Java 配置和注解配置组合，而 Spring Boot 不需要任何 xml 配置即可实现 Spring 的所有配置。

## Spring Boot 的优缺点

#### 1）优点

*   快速构建项目。
*   对主流开发框架的无配置集成。
*   项目可独立运行，无须外部依赖 Servlet 容器。
*   提供运行时的应用监控。
*   极大地提高了开发、部署效率。
*   与云计算的天然集成。

#### 2）缺点

*   版本迭代速度很快，一些模块改动很大。
*   由于不用自己做配置，报错时很难定位。
*   网上现成的解决方案比较少。