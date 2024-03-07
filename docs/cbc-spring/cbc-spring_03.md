# Spring 体系结构详解

> 原文：[`c.biancheng.net/view/4242.html`](http://c.biancheng.net/view/4242.html)

Spring 框架采用分层架构，根据不同的功能被划分成了多个模块，这些模块大体可分为 Data Access/Integration、Web、AOP、Aspects、Messaging、Instrumentation、Core Container 和 Test，如图 1 所示。![Spring 的体系结构](img/a36d0e1c8ebbcfd57305897af40266b7.png)
图 1  Spring 的体系结构
图 1 中包含了 Spring 框架的所有模块，这些模块可以满足一切企业级应用开发的需求，在开发过程中可以根据需求有选择性地使用所需要的模块。下面分别对这些模块的作用进行简单介绍。

#### 1\. Data Access/Integration（数据访问／集成）

数据访问/集成层包括 JDBC、ORM、OXM、JMS 和 Transactions 模块，具体介绍如下。

*   JDBC 模块：提供了一个 JDBC 的抽象层，大幅度减少了在开发过程中对数据库操作的编码。
*   ORM 模块：对流行的对象关系映射 API，包括 JPA、JDO、Hibernate 和 iBatis 提供了的集成层。
*   OXM 模块：提供了一个支持对象/XML 映射的抽象层实现，如 JAXB、Castor、XMLBeans、JiBX 和 XStream。
*   JMS 模块：指 Java 消息服务，包含的功能为生产和消费的信息。
*   Transactions 事务模块：支持编程和声明式事务管理实现特殊接口类，并为所有的 POJO。

#### 2\. Web 模块

Spring 的 Web 层包括 Web、Servlet、Struts 和 Portlet 组件，具体介绍如下。

*   Web 模块：提供了基本的 Web 开发集成特性，例如多文件上传功能、使用的 Servlet 监听器的 IoC 容器初始化以及 Web 应用上下文。
*   Servlet 模块：包括 Spring 模型—视图—控制器（MVC）实现 Web 应用程序。
*   Struts 模块：包含支持类内的 Spring 应用程序，集成了经典的 Struts Web 层。
*   Portlet 模块：提供了在 Portlet 环境中使用 MV C 实现，类似 Web-Servlet 模块的功能。

#### 3\. Core Container（核心容器）

Spring 的核心容器是其他模块建立的基础，由 Beans 模块、Core 核心模块、Context 上下文模块和 Expression Language 表达式语言模块组成，具体介绍如下。

*   Beans 模块：提供了 BeanFactory，是工厂模式的经典实现，Spring 将管理对象称为 Bean。
*   Core 核心模块：提供了 Spring 框架的基本组成部分，包括 IoC 和 DI 功能。
*   Context 上下文模块：建立在核心和 Beans 模块的基础之上，它是访问定义和配置任何对象的媒介。ApplicationContext 接口是上下文模块的焦点。
*   Expression Language 模块：是运行时查询和操作对象图的强大的表达式语言。

#### 4\. 其他模块

Spring 的其他模块还有 AOP、Aspects、Instrumentation 以及 Test 模块，具体介绍如下。

*   AOP 模块：提供了面向切面编程实现，允许定义方法拦截器和切入点，将代码按照功能进行分离，以降低耦合性。
*   Aspects 模块：提供与 AspectJ 的集成，是一个功能强大且成熟的面向切面编程（AOP）框架。
*   Instrumentation 模块：提供了类工具的支持和类加载器的实现，可以在特定的应用服务器中使用。
*   Test 模块：支持 Spring 组件，使用 JUnit 或 TestNG 框架的测试。