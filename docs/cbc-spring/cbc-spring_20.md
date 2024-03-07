# Spring JDBCTemplate 简介

> 原文：[`c.biancheng.net/view/4280.html`](http://c.biancheng.net/view/4280.html)

Spring 框架针对数据库开发中的应用提供了 JDBCTemplate 类，该类是 Spring 对 JDBC 支持的核心，它提供了所有对数据库操作功能的支持。

Spring 框架提供的 JDBC 支持主要由四个包组成，分别是 core（核心包）、object（对象包）、dataSource（数据源包）和 support（支持包），org.springframework.jdbc.core.JdbcTemplate 类就包含在核心包中。作为 Spring JDBC 的核心，JdbcTemplate 类中包含了所有数据库操作的基本方法。

JdbcTemplate 类继承自抽象类 JdbcAccessor，同时实现了 JdbcOperations 接口。其直接父类 JdbcAccessor 为子类提供了一些访问数据库时使用的公共属性，具体介绍如下。

#### 1）DataSource

其主要功能是获取数据库连接，具体实现时还可以引入对数据库连接的缓冲池和分布式事务的支持，它可以作为访问数据库资源的标准接口。

#### 2）SQLExceptionTranslator

org.springframework.jdbc.support.SQLExceptionTranslator 接口负责对 SQLException 进行转译工作。通过必要的设置或者获取 SQLExceptionTranslator 中的方法，可以使 JdbcTemplate 在需要处理 SQLException 时，委托 SQLExceptionTranslator 的实现类完成相关的转译工作。

JdbcOperations 接口定义了在 JdbcTemplate 类中可以使用的操作集合，包括添加、修改、查询和删除等操作。

Spring 中 JDBC 的相关信息是在 Spring 配置文件中完成的，其配置模板如下所示：

```

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http:/www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd"> 

    <!-- 配置数据源 --> 
    <bean id="dataSource" class="org.springframework.jdbc.dataSource.DriverManagerDataSource">
        <!--数据库驱动-->
        <property name="driverClassName" value="com.mysql.jdbc.Driver" /> 
        <!--连接数据库的 url-->
        <property name= "url" value="jdbc:mysql://localhost/spring" />
        <!--连接数据库的用户名-->
        <property name="username" value="root" />
        <!--连接数据库的密码-->
        <property name="password" value="root" />
    </bean>
    <!--配置 JDBC 模板-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.jdbcTemplate">
        <!--默认必须使用数据源-->
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!--配置注入类-->
    <bean id="xxx" class="xxx">
        <property name="jdbcTemplate" ref="jdbcTemplate"/>
    </bean>
    ...
</beans>
```

在上述代码中，定义了三个 Bean，分别是 dataSource、jdbcTemplate 和需要注入类的 Bean。其中 dataSource 对应的是 DriverManagerDataSource 类，用于对数据源进行配置；jdbcTemplate 对应 JdbcTemplate 类，该类中定义了 JdbcTemplate 的相关配置。

在 dataSource 中，定义了四个连接数据库的属性，如表 1 所示。

表 1 dataSource 的四个属性

| 属性名 | 含义 |
| --- | --- |
| driverClassName | 所使用的驱动名称，对应驱动 JAR 包中的 Driver 类 |
| url | 数据源所在地址 |
| username | 访问数据库的用户名 |
| password | 访问数据库的密码 |

表 1 中的属性值需要根据数据库类型或者机器配置的不同进行相应设置。如果数据库类型不同，则需要更改驱动名称。如果数据库不在本地，则需要将 localhost 替换成相应的主机 IP。

在定义 jdbcTemplate 时，需要将 dataSource 注入 jdbcTemplate 中。而在其他的类中要使用 jdbcTemplate，也需要将 jdbcTemplate 注入使用类中（通常注入 dao 类中）。

在 JdbcTemplate 类中，提供了大量的查询和更新数据库的方法，如 query()、update() 等。关于这些方法的具体使用，将在下一节的案例中讲解，此处读者了解即可。