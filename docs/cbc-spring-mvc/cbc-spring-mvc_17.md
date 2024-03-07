# Spring MVC 拦截器（Interceptor）的配置及使用

> 原文：[`c.biancheng.net/view/4431.html`](http://c.biancheng.net/view/4431.html)

在开发一个网站时可能有这样的需求：某些页面只希望几个特定的用户浏览。对于这样的访问权限控制，应该如何实现呢？拦截器就可以实现上述需求。在 Struts 2 框架中，拦截器是其重要的组成部分，Spring MVC 框架也提供了拦截器功能。

Spring MVC 的拦截器（Interceptor）与 Java Servlet 的过滤器（Filter）类似，它主要用于拦截用户的请求并做相应的处理，通常应用在权限验证、记录请求信息的日志、判断用户是否登录等功能上。

## 拦截器的定义

在 Spring MVC 框架中定义一个拦截器需要对拦截器进行定义和配置，定义一个拦截器可以通过两种方式：一种是通过实现 HandlerInterceptor 接口或继承 HandlerInterceptor 接口的实现类来定义；另一种是通过实现 WebRequestInterceptor 接口或继承 WebRequestInterceptor 接口的实现类来定义。

本节以实现 HandlerInterceptor 接口的定义方式为例讲解自定义拦截器的使用方法。示例代码如下：

```

package interceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

public class TestInterceptor implements HandlerInterceptor {
    @Override
    public void afterCompletion(HttpServletRequest request,
            HttpServletResponse response, Object handler, Exception ex)
            throws Exception {
        System.out.println("afterCompletion 方法在控制器的处理请求方法执行完成后执行，即视图渲染结束之后执行");

    }

    @Override
    public void postHandle(HttpServletRequest request,
            HttpServletResponse response, Object handler,
            ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle 方法在控制器的处理请求方法调用之后，解析视图之前执行");
    }

    @Override
    public boolean preHandle(HttpServletRequest request,
            HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle 方法在控制器的处理请求方法调用之后，解析视图之前执行");
        return false;
    }
}
```

在上述拦截器的定义中实现了 HandlerInterceptor 接口，并实现了接口中的 3 个方法。有关这 3 个方法的描述如下。

*   preHandle 方法：该方法在控制器的处理请求方法前执行，其返回值表示是否中断后续操作，返回 true 表示继续向下执行，返回 false 表示中断后续操作。
*   postHandle 方法：该方法在控制器的处理请求方法调用之后、解析视图之前执行，可以通过此方法对请求域中的模型和视图做进一步的修改。
*   afterCompletion 方法：该方法在控制器的处理请求方法执行完成后执行，即视图渲染结束后执行，可以通过此方法实现一些资源清理、记录日志信息等工作。

## 拦截器的配置

让自定义的拦截器生效需要在 Spring MVC 的配置文件中进行配置，配置示例代码如下：

```

<!-- 配置拦截器 -->
<mvc:interceptors>
    <!-- 配置一个全局拦截器，拦截所有请求 -->
    <bean class="interceptor.TestInterceptor" /> 
    <mvc:interceptor>
        <!-- 配置拦截器作用的路径 -->
        <mvc:mapping path="/**" />
        <!-- 配置不需要拦截作用的路径 -->
        <mvc:exclude-mapping path="" />
        <!-- 定义<mvc:interceptor>元素中，表示匹配指定路径的请求才进行拦截 -->
        <bean class="interceptor.Interceptor1" />
    </mvc:interceptor>
    <mvc:interceptor>
        <!-- 配置拦截器作用的路径 -->
        <mvc:mapping path="/gotoTest" />
        <!-- 定义在<mvc: interceptor>元素中，表示匹配指定路径的请求才进行拦截 -->
        <bean class="interceptor.Interceptor2" />
    </mvc:interceptor>
</mvc:interceptors>
```

在上述示例代码中，<mvc：interceptors> 元素用于配置一组拦截器，其子元素 <bean> 定义的是全局拦截器，即拦截所有的请求。

<mvc：interceptor> 元素中定义的是指定路径的拦截器，其子元素 <mvc：mapping> 用于配置拦截器作用的路径，该路径在其属性 path 中定义。

如上述示例代码中，path 的属性值“/**”表示拦截所有路径，“/gotoTest”表示拦截所有以“/gotoTest”结尾的路径。如果在请求路径中包含不需要拦截的内容，可以通过 <mvc：exclude-mapping> 子元素进行配置。

需要注意的是，<mvc：interceptor> 元素的子元素必须按照 <mvc：mapping.../>、<mvc：exclude-mapping.../>、<bean.../> 的顺序配置。