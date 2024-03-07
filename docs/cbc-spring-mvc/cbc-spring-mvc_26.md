# Spring MVC 的国际化

> 原文：[`c.biancheng.net/view/4459.html`](http://c.biancheng.net/view/4459.html)

Spring MVC 的国际化是建立在 Java 国际化的基础之上的，Spring MVC 框架的底层国际化与 Java 国际化是一致的，作为一个良好的 MVC 框架，Spring MVC 将 Java 国际化的功能进行了封装和简化，开发者使用起来会更加简单、快捷。

由《Java 国际化概念和使用介绍》教程可知国际化和本地化应用程序时需要具备以下两个条件：

*   将文本信息放到资源属性文件中。
*   选择和读取正确位置的资源属性文件。

下面讲解第二个条件的实现。

## Spring MVC 加载资源属性文件

在 Spring MVC 中不能直接使用 ResourceBundle 加载资源属性文件，而是利用 bean（messageSource）告知 Spring MVC 框架要将资源属性文件放到哪里。示例代码如下：

```

<bean id="messageSource" class="org.springframework.context.support.ReloadableResourceBundleM essageSource">
    <property name="basenames">
        <list>
            <value>/WEB-INF/resource/messages</value>
            <value>/WEB-INF/resource/labels</value>
        </list>
    </property>
</bean>
```

## 语言区域的选择

在 Spring MVC 中可以使用语言区域解析器 bean 选择语言区域，该 bean 有 3 个常见实现，即 AcceptHeaderLocaleResolver、SessionLocaleResolver 和 CookieLocaleResolver。

#### 1）AcceptHeaderLocaleResolver

根据浏览器 Http Header 中的 accept-language 域设定（accept-language 域中一般包含了当前操作系统的语言设定，可通过 HttpServletRequest.getLocale 方法获得此域的内容）。

改变 Locale 是不支持的，即不能调用 LocaleResolver 接口的 setLocale（HttpServletRequest request, HttpServletResponse response, Locale locale）方法设置 Locale。

#### 2）SessionLocaleResolver

根据用户本次会话过程中的语言设定决定语言区域（例如用户进入首页时选择语言种类，则此次会话周期内统一使用该语言设定）。

#### 3）CookieLocaleResolver

根据 Cookie 判定用户的语言设定（Cookie 中保存着用户前一次的语言设定参数）。

由上述分析可知，SessionLocaleResolver 实现比较方便用户选择喜欢的语言种类，教程中使用该方法进行国际化实现。

下面是使用 SessionLocaleResolver 实现的 bean 定义：

<bean id="localeResolver" class="org.springframework.web.servlet.il8n.SessionLocaleResolver">
    <property name="defaultLocale" value="zh_CN"></property>
</bean>

如果采用基于 SessionLocaleResolver 和 CookieLocaleResolver 的国际化实现，必须配置 LocaleChangeInterceptor 拦截器，示例代码如下：

<mvc:interceptors>
    <bean class="org.springframework.web.servlet.il8n.LocaleChangeInterceptor"/>
</mvc:interceptors>

## 使用 message 标签显示国际化信息

在 Spring MVC 框架中可以使用 Spring 的 message 标签在 JSP 页面中显示国际化消息。在使用 message 标签时需要在 JSP 页面的最前面使用 taglib 指令声明 spring 标签，代码如下：

<%@taglib prefix="spring" uri="http://www.springframework.org/tags"%>

message 标签有以下常用属性。

*   code：获得国际化消息的 key。
*   arguments：代表该标签的参数。如果替换消息中的占位符，示例代码为“<spring：message code="third" arguments="888，999" />”，third 对应的消息有两个占位符 {0} 和 {1}。
*   argumentSeparator：用来分隔该标签参数的字符，默认为逗号。
*   text：code 属性不存在，或指定的 key 无法获取消息时所显示的默认文本信息。