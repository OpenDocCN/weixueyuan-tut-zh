# Spring MVC 视图解析器

> 原文：[`c.biancheng.net/view/4396.html`](http://c.biancheng.net/view/4396.html)

Spring 视图解析器是 Spring MVC 中的重要组成部分，用户可以在配置文件中定义 Spring MVC 的一个视图解析器（ViewResolver），示例代码如下：

```

<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" >
    <!--前缀-->
    <property name="prefix" value="/WEB-INF/jsp/"/>
    <!--后缀-->
    <property name="suffix" value=".jsp"/>
</bean>
```

上述视图解析器配置了前缀和后缀两个属性，因此《第一个 Spring MVC 应用》教程中的 RegisterController 和 LoginController 控制器类的视图路径仅需提供 register 和 login，视图解析器将会自动添加前缀和后缀。

InternalResourceViewResolver 是 URLBasedViewResolver 的子类，所以 URLBasedViewResolver 支持的特性它都支持。

在实际应用中 InternalResourceViewResolver 也是使用的最广泛的一个视图解析器。那么 InternalResourceViewResolver 有什么自己独有的特性呢？

单从字面意思来看，我们可以把 InternalResourceViewResolver 解释为内部资源视图解析器，这就是 InternalResourceViewResolver 的一个特性。

InternalResourceViewResolver 会把返回的视图名称都解析为 InternalResourceView 对象，InternalResourceView 会把 Controller 处理器方法返回的模型属性都存放到对应的 request 属性中，然后通过 RequestDispatcher 在服务器端把请求 forword 重定向到目标 URL。

比如在 InternalResourceViewResolver 中定义了 prefix=/WEB-INF/，suffix=.jsp，然后请求的 Controller 处理器方法返回的视图名称为 login，那么这个时候 InternalResourceViewResolver 就会把 login 解析为一个 InternalResourceView 对象，先把返回的模型属性都存放到对应的 HttpServletRequest 属性中，然后利用 RequestDispatcher 在服务器端把请求 forword 到 /WEB-INF/test.jsp。

这就是 InternalResourceViewResolver 一个非常重要的特性，我们都知道存放在 /WEB-INF/ 下面的内容是不能直接通过 request 请求的方式请求到的，为了安全性考虑，我们通常会把 jsp 文件放在 WEB-INF 目录下，而 InternalResourceView 在服务器端跳转的方式可以很好的解决这个问题。