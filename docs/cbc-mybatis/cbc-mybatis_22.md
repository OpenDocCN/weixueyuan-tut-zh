# MyBatis 与 Spring 的整合步骤

> 原文：[`c.biancheng.net/view/4311.html`](http://c.biancheng.net/view/4311.html)

从之前的代码中可以看出直接使用 MyBatis 框架的 SqlSession 访问数据库并不简便。MyBatis 框架的重点是 SQL 映射文件，为方便后续学习，本节讲解 MyBatis 与 Spring 的整合。教程的后续讲解中将使用整合后的框架进行演示。

## 导入相关 JAR 包

实现 MyBatis 与 Spring 的整合需要导入相关 JAR 包，包括 MyBatis、Spring 以及其他 JAR 包。

#### 1）MyBatis 框架所需的 JAR 包

MyBatis 框架所需的 JAR 包包括它的核心包和依赖包，包的详情可参考《第一个 MyBatis 程序》。

#### 2）Spring 框架所需的 JAR 包

Spring 框架所需的 JAR 包包括它的核心模块 JAR、AOP 开发使用的 JAR、JDBC 和事务的 JAR 包（其中依赖包不需要再导入，因为 MyBatis 已提供），具体如下：

*   aopalliance-1.0.jar
*   aspectjweaver-1.6.9.jar
*   spring-aop-3.2.13.RELEASE.jar
*   spring-aspects-3.2.13.RELEASE.jar
*   spring-beans-3.2.13.RELEASE.jar
*   spring-context-3.2.13.RELEASE.jar
*   spring-core-3.2.13.RELEASE.jar
*   spring-expression-3.2.13.RELEASE.jar
*   spring-jdbc-3.2.13.RELEASE.jar
*   spring-tx-3.2.13.RELEASE.jar

#### 3）MyBatis 与 Spring 整合的中间 JAR 包

该中间 JAR 包的版本为 mybatis-spring-1.3.1.jar，此版本可以从网址“[`mvnrepository.com/artifact/org.mybatis/mybatis-spring/1.3.1`](http://mvnrepository.com/artifact/org.mybatis/mybatis-spring/1.3.1)”下载。

#### 4）数据库驱动 JAR 包

教程所使用的 MySQL 数据库驱动包为 mysql-connector-java-5.1.25-bin.jar。

#### 5）数据源所需的 JAR 包

在整合时使用的是 DBCP 数据源，需要准备 DBCP 和连接池的 JAR 包。

本教程所用版本的 DBCP 的 JAR 包为 commons-dbcp2-2.2.0.jar，可以从网址“[htttp://commons.apache.org/proper/commons-dbcp/download_dbcp.cgi](http://htttp://commons.apache.org/proper/commons-dbcp/download_dbcp.cgi)”下载。

最新版本的连接池的 JAR 包为 commons-pool2-2.5.0.jar，可以从网址“[`commons.apache.org/proper/commons-pool/download_pool.cgi`](http://commons.apache.org/proper/commons-pool/download_pool.cgi)”下载。

## 在 Spring 中配置 MyBatis 工厂

通过与 Spring 的整合，MyBatis 的 SessionFactory 交由 Spring 来构建，在构建时需要在 Spring 的配置文件中添加如下代码：

```

<!--配置数据源-->
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver" />
    <property name="url" value="jdbc:mysql://127.0.0.1:3306/springtest?seUnicode=true&amp;characterEncoding=utf-8" />
    <property name="username" value="root" />
    <property name="password" value="1128" />
    <!-- 最大连接数 -->
    <property name="maxTotal" value="30"/>
    <!-- 最大空闲连接数 -->
    <property name="maxIdle" value="10"/>
    <!-- 初始化连接数 -->
    <property name="initialSize" value="5"/>
</bean>
<!-- 配置 SqlSessionFactoryBean -->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <!-- 引用数据源组件 -->
    <property name="dataSource" ref="dataSource" />
    <!-- 引用 MyBatis 配置文件中的配置 -->
    <property name="configLocation" value="classpath:mybatis-config.xml" />
</bean>
```

## 使用 Spring 管理 MyBatis 的数据操作接口

使用 Spring 管理 MyBatis 数据操作接口的方式有多种，其中最常用、最简洁的一种是基于 MapperScannerConfigurer 的整合。该方式需要在 Spring 的配置文件中加入以下内容：

```

<!-- Mapper 代理开发，使用 Spring 自动扫描 MyBatis 的接口并装配 （Sprinh 将指定包中的所有被@Mapper 注解标注的接口自动装配为 MyBatis 的映射接口） -->
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <!-- mybatis-spring 组件的扫描器，com.dao 只需要接口（接口方法与 SQL 映射文件中的相同） -->
    <property name="basePackage" value="com.dao" />
    <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
</bean>
```