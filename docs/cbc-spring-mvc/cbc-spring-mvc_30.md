# Spring MVC 使用 HandlerExceptionResolver 接口异常处理

> 原文：[`c.biancheng.net/view/4469.html`](http://c.biancheng.net/view/4469.html)

org.springframework.web.servlet.HandlerExceptionResolver 接口用于解析请求处理过程中所产生的异常。开发者可以开发该接口的实现类进行 Spring MVC 应用的异常统一处理。

在 springMVCDemo10 应用的 exception 包中创建一个 HandlerExceptionResolver 接口的实现类 MyExceptionHandler，具体代码如下：

```

package exception;

import java.sql.SQLException;
import java.util.HashMap;
import java.util.Map;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.web.servlet.HandlerExceptionResolver;
import org.springframework.web.servlet.ModelAndView;

public class MyExceptionHandler implements HandlerExceptionResolver {

    @Override
    public ModelAndView resolveException(HttpServletRequest arg0,
            HttpServletResponse arg1, Object arg2, Exception arg3) {
        Map<String, Object> model = new HashMap<String, Object>();
        model.put("ex", arg3);
        // 根据不同错误转向不同页面（统一处理），即异常与 View 的对应关系
        if (arg3 instanceof MyException) {
            return new ModelAndView("my-error", model);
        } else if (arg3 instanceof SQLException) {
            return new ModelAndView("sql-error", model);
        } else {
            return new ModelAndView("error", model);
        }
    }
}
```

需要将实现类 MyExceptionHandler 在配置文件中托管给 Spring MVC 框架才能进行异常的统一处理，配置代码为 <bean class="exception.MyExceptionHandler"/>。

在实现 HandlerExceptionResolver 接口统一处理异常时将配置文件的代码修改如下：

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
    <!--托管 MyExceptionHandler-->
    <bean class="exception.MyExceptionHandler"/>
</beans>
```

发布 springMVCDemo10 应用到 Tomcat 服务器并启动服务器，然后即可通过地址“http://localhost:8080/springMVCDemo10/”测试应用。