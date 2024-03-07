# Spring 自动装配 Bean

> 原文：[`c.biancheng.net/view/4267.html`](http://c.biancheng.net/view/4267.html)

除了使用 XML 和 Annotation 的方式装配 Bean 以外，还有一种常用的装配方式——自动装配。自动装配就是指 Spring 容器可以自动装配（autowire）相互协作的 Bean 之间的关联关系，将一个 Bean 注入其他 Bean 的 Property 中。

要使用自动装配，就需要配置 <bean> 元素的 autowire 属性。autowire 属性有五个值，具体说明如表 1 所示。

表 1 autowire 的属性和作用

| 名称 | 说明 |
| --- | --- |
| byName | 根据 Property 的 name 自动装配，如果一个 Bean 的 name 和另一个 Bean 中的 Property 的 name 相同，则自动装配这个 Bean 到 Property 中。 |
| byType | 根据 Property 的数据类型（Type）自动装配，如果一个 Bean 的数据类型兼容另一个 Bean 中 Property 的数据类型，则自动装配。 |
| constructor | 根据构造方法的参数的数据类型，进行 byType 模式的自动装配。 |
| autodetect | 如果发现默认的构造方法，则用 constructor 模式，否则用 byType 模式。 |
| no | 默认情况下，不使用自动装配，Bean 依赖必须通过 ref 元素定义。 |

下面通过修改《Spring 基于 Annotation 装配 Bean》中的案例演示如何实现自动装配。首先将 applicationContext.xml 配置文件修改成自动装配形式，如下所示。

```

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:p="http://www.springframework.org/schema/p" 
    xmlns:tx="http://www.springframework.org/schema/tx"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="  
            http://www.springframework.org/schema/beans 
            http://www.springframework.org/schema/beans/spring-beans-2.5.xsd  
            http://www.springframework.org/schema/aop 
            http://www.springframework.org/schema/aop/spring-aop-2.5.xsd  
            http://www.springframework.org/schema/tx 
            http://www.springframework.org/schema/tx/spring-tx-2.5.xsd
            http://www.springframework.org/schema/context
            http://www.springframework.org/schema/context/spring-context.xsd">
    <bean id="personDao" class="com.mengma.annotation.PersonDaoImpl" />
    <bean id="personService" class="com.mengma.annotation.PersonServiceImpl"
        autowire="byName" />
    <bean id="personAction" class="com.mengma.annotation.PersonAction"
        autowire="byName" />
</beans>
```

在上述配置文件中，用于配置 personService 和 personAction 的 <bean> 元素中除了 id 和 class 属性以外，还增加了 autowire 属性，并将其属性值设置为 byName（按属性名称自动装配）。

默认情况下，配置文件中需要通过 ref 装配 Bean，但设置了 autowire="byName"，Spring 会在配置文件中自动寻找与属性名字 personDao 相同的 <bean>，找到后，通过调用 setPersonDao（PersonDao personDao）方法将 id 为 personDao 的 Bean 注入 id 为 personService 的 Bean 中，这时就不需要通过 ref 装配了。

使用 JUnit 再次运行测试类中的 test() 方法，控制台的显示结果如图 1 所示。

![运行结果](img/260360a0e25dfeab9683eda8bd0cc31b.png)
图 1  运行结果
从图 1 的输出结果中可以看出，使用自动装配的方式同样完成了依赖注入。