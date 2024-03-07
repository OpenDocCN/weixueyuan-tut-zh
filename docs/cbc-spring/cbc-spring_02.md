# Java Spring 框架是什么？它有哪些好处？

> 原文：[`c.biancheng.net/view/4241.html`](http://c.biancheng.net/view/4241.html)

Spring 是另一个主流的 Java Web 开发框架，该框架是一个轻量级的应用框架，具有很高的凝聚力和吸引力。Spring 框架因其强大的功能以及卓越的性能而受到众多开发人员的喜爱。

Spring 是分层的 Java SE/EE full-stack 轻量级开源框架，以 IoC（Inverse of Control，控制反转）和 AOP（Aspect Oriented Programming，面向切面编程）为内核，使用基本的 JavaBean 完成以前只可能由 EJB 完成的工作，取代了 EJB 臃肿和低效的开发模式。

在实际开发中，通常服务器端采用三层体系架构，分别为表现层（web）、业务逻辑层（service）、持久层（dao）。

Spring 对每一层都提供了技术支持，在表现层提供了与 Struts2 框架的整合，在业务逻辑层可以管理事务和记录日志等，在持久层可以整合 Hibernate 和 JdbcTemplate 等技术。

从设计上看，Spring 框架给予了 Java 程序员更高的自由度，对业界的常见问题也提供了良好的解决方案，因此，在开源社区受到了广泛的欢迎，并且被大部分公司作为 Java 项目开发的首选框架。

Spring 具有简单、可测试和松耦合等特点，不仅可以用于服务器端的开发，也可以应用于任何 Java 应用的开发中。Spring 框架的主要优点具体如下。

#### 1）方便解耦，简化开发

Spring 就是一个大工厂，可以将所有对象的创建和依赖关系的维护交给 Spring 管理。

#### 2）方便集成各种优秀框架

Spring 不排斥各种优秀的开源框架，其内部提供了对各种优秀框架（如 Struts2、Hibernate、MyBatis 等）的直接支持。

#### 3）降低 Java EE API 的使用难度

Spring 对 Java EE 开发中非常难用的一些 API（JDBC、JavaMail、远程调用等）都提供了封装，使这些 API 应用的难度大大降低。

#### 4）方便程序的测试

Spring 支持 JUnit4，可以通过注解方便地测试 Spring 程序。

#### 5）AOP 编程的支持

Spring 提供面向切面编程，可以方便地实现对程序进行权限拦截和运行监控等功能。

#### 6）声明式事务的支持

只需要通过配置就可以完成对事务的管理，而无须手动编程。