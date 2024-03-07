# Spring MVC 使用 SimpleMappingExceptionResolver 类异常处理

> 原文：[`c.biancheng.net/view/4468.html`](http://c.biancheng.net/view/4468.html)

使用 org.springframework.web.servlet.handler.SimpleMappingExceptionResolver 类统一处理异常时需要在配置文件中提前配置异常类和 View 的对应关系。配置文件 springmvc-servlet.xml 的具体代码如下：

```

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring 一 beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">
    <!-- 使用扫描机制扫描包 -->
    <context:component-scan base-package="controller" />
    <context:component-scan base-package="service" />
    <context:component-scan base-package="dao" />
    <!-- 配置视图解析器 -->
    <bean
        class="org.springframework.web.servlet.view.InternalResourceViewResolver"
        id="internalResourceViewResolver">
        <!--前缀 -->
        <property name="prefix" value="/WEB-INF/jsp/" />
        <!-- 后缀 -->
        <property name="suffix" value=".jsp" />
    </bean>
    <!--SimpleMappingExceptionResolver（异常类与 View 的对应关系） -->
    <bean
        class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
        <!-- 定义默认的异常处理页面，当该异常类型注册时使用 -->
        <property name="defaultErrorView" value="error"></property>
        <!-- 定义异常处理页面用来获取异常信息的变量名，默认名为 exception -->
        <property name="exceptionAttribute" value="ex"></property>
        <!-- 定义需要特殊处理的异常，用类名或完全路径名作为 key，异常页名作为值 -->
        <property name="exceptionMappings">
            <props>
                <prop key="exception.MyException">my-error</prop>
                <prop key="java.sql.SQLException">sql-error</prop>
                <!-- 在这里还可以继续扩展对不同异常类型的处理 -->
            </props>
        </property>
    </bean>
</beans>
```

在配置完成后就可以通过 SimpleMappingExceptionResolver 异常处理器统一处理 《Spring MVC 统一异常处理的 3 种方式（附带实例）》中的异常。

发布 springMVCDemo10 应用到 Tomcat 服务器并启动服务器，然后即可通过地址“http://localhost:8080/springMVCDemo10/”测试应用。