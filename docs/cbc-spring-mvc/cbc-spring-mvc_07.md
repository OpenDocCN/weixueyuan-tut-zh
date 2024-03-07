# Spring MVC 获取参数的几种常见方式

> 原文：[`c.biancheng.net/view/4399.html`](http://c.biancheng.net/view/4399.html)

Controller 接收请求参数的方式有很多种，有的适合 get 请求方式，有的适合 post 请求方式，有的两者都适合。下面分别介绍这些方式，读者可以根据实际情况选择合适的接收方式。

## 通过实体 Bean 接收请求参数

通过一个实体 Bean 来接收请求参数，适用于 get 和 post 提交请求方式。需要注意的是，Bean 的属性名称必须与请求参数名称相同。下面通过具体应用 springMVCDemo02 讲解“通过实体 Bean 接收请求参数”。

#### 1）创建首页面

在 springMVCDemo02 应用的 WebContent 目录下修改 index.jsp 页面，代码如下：

```

<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
    没注册的用户，请<a href="${pageContext.request.contextPath }/index/register"> 注册</a>！
  <br/>
    已注册的用户，去<a href="${pageContext.request.contextPath }/index/login"> 登录</a>！
</body>
</html>
```

#### 2）完善配置文件

完善配置文件 springmvc-servlet.xml，代码如下：

```

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
    xmlns:p="http://www.springframework.org/schema/p" xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <!-- 使用扫描机制扫描控制器类，控制器类都在 controller 包及其子包下 -->
    <context:component-scan base-package="controller" />
    <mvc:annotation-driven />
    <!-- annotation-driven 用于简化开发的配置，注解 DefaultAnnotationHandlerMapping 和 AnnotationMethodHandlerAdapter -->
    <!-- 使用 resources 过滤掉不需要 dispatcherservlet 的资源（即静态资源，例如 css、js、html、images）。
        在使用 resources 时必须使用 annotation-driven，否则 resources 元素会阻止任意控制器被调用 -->
    <!-- 允许 css 目录下的所有文件可见 -->
    <mvc:resources location="/css/" mapping="/css/**" />
    <!-- 允许 html 目录下的所有文件可见 -->
    <mvc:resources location="/html/" mapping="/html/**" />
    <!-- 允许 css 目录下的所有文件可见 -->
    <mvc:resources location="/images/" mapping="/images/**" />
    <!-- 完成视图的对应 -->
    <!-- 对转向页面的路径解析。prefix：前缀， suffix：后缀 -->
    <bean
        class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/" />
        <property name="suffix" value=".jsp" />
    </bean>
</beans>
```

#### 3）创建 POJO 实体类

在 springMVCDemo02 应用的 src 目录下创建 pojo 包，并在该包中创建实体类 UserForm，代码如下：

```

package pojo;

public class UserForm {
    private String uname; // 与请求参数名称相同
    private String upass;
    private String reupass;

    // 省略 getter 和 setter 方法
}
```

#### 4）创建控制器类

在 springMVCDemo02 应用的 controller 包中创建控制器类 IndexController 和 UserController。

IndexController 的代码如下：

```

package controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/index")
public class IndexController {
    @RequestMapping("/login")
    public String login() {
        return "login"; // 跳转到/WEB-INF/jsp 下的 login.jsp
    }

    @RequestMapping("/register")
    public String register() {
        return "register";
    }
}
```

UserController 的代码如下：

```

package controller;

import javax.servlet.http.HttpSession;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import pojo.UserForm;
import com.sun.org.apache.commons.logging.Log;
import com.sun.org.apache.commons.logging.LogFactory;

@Controller
@RequestMapping("/user")
public class UserController {
    // 得到一个用来记录日志的对象，这样在打印信息的时候能够标记打印的是哪个类的信息
    private static final Log logger = LogFactory.getLog(UserController.class);

    /**
     * 处理登录 使用 UserForm 对象(实体 Bean) user 接收注册页面提交的请求参数
     */
    @RequestMapping("/login")
    public String login(UserForm user, HttpSession session, Model model) {
        if ("zhangsan".equals(user.getUname())
                && "123456".equals(user.getUpass())) {
            session.setAttribute("u", user);
            logger.info("成功");
            return "main"; // 登录成功，跳转到 main.jsp
        } else {
            logger.info("失败");
            model.addAttribute("messageError", "用户名或密码错误");
            return "login";
        }
    }

    /**
     * 处理注册 使用 UserForm 对象(实体 Bean) user 接收注册页面提交的请求参数
     */

    @RequestMapping("/register")
    public String register(UserForm user, Model model) {
        if ("zhangsan".equals(user.getUname())
                && "123456".equals(user.getUpass())) {
            logger.info("成功");
            return "login"; // 注册成功，跳转到 login.jsp
        } else {
            logger.info("失败");
            // 在 register.jsp 页面上可以使用 EL 表达式取出 model 的 uname 值
            model.addAttribute("uname", user.getUname());
            return "register"; // 返回 register.jsp
        }
    }
}
```

#### 5）创建页面视图

在 springMVCDemo02 应用的 /WEB-INF/jsp 目录下创建 register.jsp 和 login.jsp。

register.jsp 的核心代码如下：

```

<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
    <form action="${pageContext.request.contextPath }/user/register" method="post" name="registForm">
        <table border=1 bgcolor="lightblue" align="center">
            <tr>
                <td>姓名：</td>
                <td>
                    <input class="textSize" type="text" name="uname" value="${uname }" />
                </td>
            </tr>
            <tr>
                <td>密码：</td>
                <td>
                    <input class="textSize" type="password" maxlength="20" name="upass" />
                </td>
            </tr>
            <tr>
                <td>确认密码：</td>
                <td>
                    <input class="textSize" type="password" maxlength="20" name="reupass" />
                </td>
            </tr>
            <tr>
                <td colspan="2" align="center">
                    <input type="button" value="注册" onclick="allIsNull() " />
                </td>
            </tr>
        </tab1e>
    </form>
</body>
</html>
```

在 register.jsp 的代码中使用了 EL 语句“$1{uname}”取出“model.addAttribute("uname"，user.getUname())”中的值。对于 EL 和 JSTL 的相关知识，读者可参考《JSP 教程》。

login.jsp 的核心代码如下：

```

<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
    <form action="${pageContext.request.contextPath }/user/login" method="post">
        <table>
            <tr>
                <td colspan="2">
                    <img src="${pageContext.request.contextPath }/images/login.gif">
                </td>
            </tr>
            <tr>
                <td>姓名：</td>
                <td>
                    <input type="text" name="uname" class="textSize">
                </td>
            </tr>
            <tr>
                <td>密码：</td>
                <td>
                    <input type="password" name="upass" class="textsize">
                </td>
            </tr>
            <tr>
                <td colspan="2">
                    <input type="image" src="${pageContext.request.contextPath }/images/ok.gif" onclick="gogo()">
                    <input type="image" src="${pageContext.request.contextPath }/images/cancel.gif" onclick="cancel()">
                </td>
            </tr>
        </table>
        ${messageError }
    </form>
</body>
</html>
```

#### 6）测试应用

运行 springMVCDemo02 应用的首页面，进行程序测试。

## 通过处理方法的形参接收请求参数

通过处理方法的形参接收请求参数也就是直接把表单参数写在控制器类相应方法的形参中，即形参名称与请求参数名称完全相同。该接收参数方式适用于 get 和 post 提交请求方式。用户可以将“通过实体 Bean 接收请求参数”部分中控制器类 UserController 中 register 方法的代码修改如下：

```

@RequestMapping("/register")
/**
* 通过形参接收请求参数，形参名称与请求参数名称完全相同
*/
public String register(String uname,String upass,Model model) {
    if ("zhangsan".equals(uname)
            && "123456".equals(upass)) {
        logger.info("成功");
        return "login"; // 注册成功，跳转到 login.jsp
    } else {
        logger.info("失败");
        // 在 register.jsp 页面上可以使用 EL 表达式取出 model 的 uname 值
        model.addAttribute("uname", uname);
        return "register"; // 返回 register.jsp
    }
}
```

## 通过 HttpServletRequest 接收请求参数

通过 HttpServletRequest 接收请求参数适用于 get 和 post 提交请求方式，可以将“通过实体 Bean 接收请求参数”部分中控制器类 UserController 中 register 方法的代码修改如下：

```

@RequestMapping("/register")
/**
* 通过 HttpServletRequest 接收请求参数
*/
public String register(HttpServletRequest request,Model model) {
    String uname = request.getParameter("uname");
    String upass = request.getParameter("upass");
    if ("zhangsan".equals(uname)
            && "123456".equals(upass)) {
        logger.info("成功");
        return "login"; // 注册成功，跳转到 login.jsp
    } else {
        logger.info("失败");
        // 在 register.jsp 页面上可以使用 EL 表达式取出 model 的 uname 值
        model.addAttribute("uname", uname);
        return "register"; // 返回 register.jsp
    }
}
```

## 通过 @PathVariable 接收 URL 中的请求参数

通过 @PathVariable 获取 URL 中的参数，控制器类示例代码如下：

```

@Controller
@RequestMapping("/user")
public class UserController {
    @RequestMapping("/user")
    // 必须节 method 属性
    /**
     * 通过@PathVariable 获取 URL 的参数
     */
    public String register(@PathVariable String uname,@PathVariable String upass,Model model) {
        if ("zhangsan".equals(uname)
                && "123456".equals(upass)) {
            logger.info("成功");
            return "login"; // 注册成功，跳转到 login.jsp
        } else {
            // 在 register.jsp 页面上可以使用 EL 表达式取出 model 的 uname 值
            model.addAttribute("uname", uname);
            return "register"; // 返回 register.jsp
        }
    }
}
```

在访问“http://localhost：8080/springMVCDemo02/user/register/zhangsan/123456”路径时，上述代码自动将 URL 中的模板变量 {uname} 和 {upass} 绑定到通过 @PathVariable 注解的同名参数上，即 uname=zhangsan、upass=123456。

## 通过 @RequestParam 接收请求参数

通过 @RequestParam 接收请求参数适用于 get 和 post 提交请求方式，可以将“通过实体 Bean 接收请求参数”部分控制器类 UserController 中 register 方法的代码修改如下：

```

@RequestMapping("/register")
/**
* 通过@RequestParam 接收请求参数
*/
public String register(@RequestParam String uname,
    @RequestParam String upass, Model model) {
    if ("zhangsan".equals(uname) && "123456".equals(upass)) {
        logger.info("成功");
        return "login"; // 注册成功，跳转到 login.jsp
    } else {
        // 在 register.jsp 页面上可以使用 EL 表达式取出 model 的 uname 值
        model.addAttribute("uname", uname);
        return "register"; // 返回 register.jsp
    }
}
```

通过 @RequestParam 接收请求参数与“通过处理方法的形参接收请求参数”部分的区别如下：当请求参数与接收参数名不一致时，“通过处理方法的形参接收请求参数”不会报 404 错误，而“通过 @RequestParam 接收请求参数”会报 404 错误。

## 通过 @ModelAttribute 接收请求参数

当 @ModelAttribute 注解放在处理方法的形参上时，用于将多个请求参数封装到一个实体对象，从而简化数据绑定流程，而且自动暴露为模型数据，在视图页面展示时使用。

而“通过实体 Bean 接收请求参数”中只是将多个请求参数封装到一个实体对象，并不能暴露为模型数据（需要使用 model.addAttribute 语句才能暴露为模型数据，数据绑定与模型数据展示后面教程中会讲解）。

通过 @ModelAttribute 注解接收请求参数适用于 get 和 post 提交请求方式，可以将“通过实体 Bean 接收请求参数”中控制器类 UserController 中 register 方法的代码修改如下：

```

@RequestMapping("/register")
public String register(@ModelAttribute("user") UserForm user) {
    if ("zhangsan".equals(uname) && "123456".equals(upass)) {
        logger.info("成功");
        return "login"; // 注册成功，跳转到 login.jsp
    } else {
        logger.info("失败");
        // 使用@ModelAttribute("user")与 model.addAttribute("user",user)的功能相同
        //register.jsp 页面上可以使用 EL 表达式${user.uname}取出 ModelAttribute 的 uname 值
        return "register"; // 返回 register.jsp
    }
}
```