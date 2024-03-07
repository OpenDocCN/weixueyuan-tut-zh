# Spring MVC 中@Controller 和@RequestMapping 注解详解

> 原文：[`c.biancheng.net/view/4397.html`](http://c.biancheng.net/view/4397.html)

在《第一个 Spring MVC 应用》教程中创建了两个传统风格的控制器，它们是实现 Controller 接口的类。传统风格的控制器不仅需要在配置文件中部署映射，而且只能编写一个处理方法，不够灵活。使用基于注解的控制器具有以下两个优点：

*   在基于注解的控制器类中可以编写多个处理方法，进而可以处理多个请求（动作），这就允许将相关的操作编写在同一个控制器类中，从而减少控制器类的数量，方便以后的维护。
*   基于注解的控制器不需要在配置文件中部署映射，仅需要使用 RequestMapping 注释类型注解一个方法进行请求处理。

在 Spring MVC 中最重要的两个注解类型是 Controller 和 RequestMapping，本节将重点介绍它们。在本节将创建一个 Spring MVC 应用 springMVCDemo02 来演示相关知识，springMVCDemo01 的 JAR 包、web.xml 与 springMVCDemo02 应用的 JAR 包、web.xml 完全一样。

## Controller 注解类型

在 Spring MVC 中使用 org.springframework.stereotype.Controller 注解类型声明某类的实例是一个控制器。例如，在 springMVCDemo02 应用的 src 目录下创建 controller 包，并在该包中创建 Controller 注解的控制器类 IndexController，示例代码如下：

```

package controller;

import org.springframework.stereotype.Controller;

/**
* “@Controller”表示 IndexController 的实例是一个控制器
*
* @Controller 相当于@Controller(@Controller) 或@Controller(value="@Controller")
*/
@Controller
public class IndexController {
    // 处理请求的方法
}
```

在 Spring MVC 中使用扫描机制找到应用中所有基于注解的控制器类，所以，为了让控制器类被 Spring MVC 框架扫描到，需要在配置文件中声明 spring-context，并使用 <context：component-scan/> 元素指定控制器类的基本包（请确保所有控制器类都在基本包及其子包下）。

例如，在 springMVCDemo02 应用的 /WEB-INF/ 目录下创建配置文件 springmvc-servlet.xml，示例代码如下：

```

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xmlns:p="http://www.springframework.org/schema/p" 
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <!-- 使用扫描机制扫描控制器类，控制器类都在 controller 包及其子包下 -->
    <context:component-scan base-package="controller" />
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/" />
        <property name="suffix" value=".jsp" />
    </bean>
</beans>
```

## RequestMapping 注解类型

在基于注解的控制器类中可以为每个请求编写对应的处理方法。那么如何将请求与处理方法一一对应呢？

需要使用 org.springframework.web.bind.annotation.RequestMapping 注解类型将请求与处理方法一一对应。

#### 1）方法级别注解

方法级别注解的示例代码如下：

```

package controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

/**
* “@Controller”表示 IndexController 的实例是一个控制器
*
* @Controller 相当于@Controller(@Controller) 或@Controller(value="@Controller")
*/
@Controller
public class IndexController {
    @RequestMapping(value = "/index/login")
    public String login() {
        /**
         * login 代表逻辑视图名称，需要根据 Spring MVC 配置
         * 文件中 internalResourceViewResolver 的前缀和后缀找到对应的物理视图
         */
        return "login";
    }

    @RequestMapping(value = "/index/register")
    public String register() {
        return "register";
    }
}
```

上述示例中有两个 RequestMapping 注解语句，它们都作用在处理方法上。注解的 value 属性将请求 URI 映射到方法，value 属性是 RequestMapping 注解的默认属性，如果只有一个 value 属性，则可以省略该属性。

用户可以使用如下 URL 访问 login 方法（请求处理方法），在访问 login 方法之前需要事先在 /WEB-INF/jsp/ 目录下创建 login.jsp。

http://localhost:8080/springMVCDemo02/index/login

#### 2）类级别注解

类级别注解的示例代码如下：

```

package controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
@Controller
@RequestMapping("/index")
public class IndexController {
    @RequestMapping("/login")
    public String login() {
        return "login";
    }

    @RequestMapping("/register")
    public String register() {
        return "register";
    }
}
```

在类级别注解的情况下，控制器类中的所有方法都将映射为类级别的请求。用户可以使用如下 URL 访问 login 方法。

http://localhost:8080/springMVCDemo02/index/login

为了方便维护程序，建议开发者采用类级别注解，将相关处理放在同一个控制器类中。例如，对商品的增、删、改、查处理方法都可以放在 GoodsOperate 控制类中。

## 编写请求处理方法

在控制类中每个请求处理方法可以有多个不同类型的参数，以及一个多种类型的返回结果。

#### 1）请求处理方法中常出现的参数类型

如果需要在请求处理方法中使用 Servlet API 类型，那么可以将这些类型作为请求处理方法的参数类型。Servlet API 参数类型的示例代码如下：

```

package controller;
import javax.servlet.http.HttpSession;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
@Controller
@RequestMapping("/index")
public class IndexController {
    @RequestMapping("/login")
    public String login(HttpSession session,HttpServletRequest request) {
        session.setAttribute("skey", "session 范围的值");
        session.setAttribute("rkey", "request 范围的值");
        return "login";
    }
}
```

除了 Servlet API 参数类型以外，还有输入输出流、表单实体类、注解类型、与 Spring 框架相关的类型等，这些类型在后续章节中使用时再详细介绍。

其中特别重要的类型是 org.springframework.ui.Model 类型，该类型是一个包含 Map 的 Spring 框架类型。在每次调用请求处理方法时 Spring MVC 都将创建 org.springframework.ui.Model 对象。Model 参数类型的示例代码如下：

```

package controller;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
@Controller
@RequestMapping("/index")
public class IndexController {
    @RequestMapping("/register")
    public String register(Model model) {
        /*在视图中可以使用 EL 表达式${success}取出 model 中的值*/
        model.addAttribute("success", "注册成功");
        return "register";
    }
}
```

#### 2）请求处理方法常见的返回类型

最常见的返回类型就是代表逻辑视图名称的 String 类型，例如前面教程中的请求处理方法。除了 String 类型以外，还有 ModelAndView、Model、View 以及其他任意的 Java 类型。