# Spring Boot 简介

> 原文：[`c.biancheng.net/view/5313.html`](http://c.biancheng.net/view/5313.html)

Spring Boot 是由 Pivotal 团队提供的全新框架，其设计目的是简化新 Spring 应用的初始搭建以及开发过程。该框架使用了特定的方式进行配置，从而使开发人员不再需要定义样板化的配置。

Spring Boot 致力于在蓬勃发展的快速应用开发领域（rapid application development）成为领导者。

在使用 Spring Boot 之前，我们需要搭建一个项目框架并配置各种第三方库的依赖，还需要在 XML 中配置很多内容。

Spring Boot 完全打破了我们之前的使用习惯，一分钟就可以创建一个 Web 开发的项目；通过 Starter 的方式轻松集成第三方的框架；去掉了 XML 的配置，全部用注解代替。

Spring Boot Starter 是用来简化 jar 包依赖的，集成一个框架只需要引入一个 Starter，然后在属性文件中配置一些值，整个集成的过程就结束了。

不得不说，Spring Boot 在内部做了很多的处理，让开发人员使用起来更加简单了。

下面笔者总结了一些使用 Spring Boot 开发的优点：

*   基于 Spring 开发 Web 应用更加容易。
*   采用基于注解方式的配置，避免了编写大量重复的 XML 配置。
*   可以轻松集成 Spring 家族的其他框架，比如 Spring JDBC、Spring Data 等。
*   提供嵌入式服务器，令开发和部署都变得非常方便。