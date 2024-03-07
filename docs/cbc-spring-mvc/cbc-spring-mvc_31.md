# Spring MVC 使用@ExceptionHandler 注解异常处理

> 原文：[`c.biancheng.net/view/4470.html`](http://c.biancheng.net/view/4470.html)

创建 BaseController 类，并在该类中使用 @ExceptionHandler 注解声明异常处理方法，具体代码如下：

```
package controller;
import java.sql.SQLException;
import javax.servlet.http.HttpServletRequest;
import org.springframework.web.bind.annotation.ExceptionHandler;
import exception.MyException;
public class BaseController {
    /** 基于@ExceptionHandler 异常处理 */
    @ExceptionHandler
    public String exception(HttpServletRequest request, Exception ex) {
        request.setAttribute("ex", ex);
        // 根据不同错误转向不同页面，即异常与 view 的对应关系
        if (ex instanceof SQLException) {
            return "sql-error";
        } else if (ex instanceof MyException) {
            return "my-error";
        } else {
            return "error";
        }
    }
}
```

将所有需要异常处理的 Controller 都继承 BaseController 类，示例代码如下：

```
@Controller
public class TestExceptionController extends BaseController{
    ...
}
```

在使用 @ExceptionHandler 注解声明统一处理异常时不需要配置任何信息，此时将配置文件的代码修改如下：

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
</beans>
```

发布 springMVCDemo10 应用到 Tomcat 服务器并启动服务器，然后即可通过地址“http://localhost:8080/springMVCDemo10/”测试应用。