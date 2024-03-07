# Spring MVC 中@ModelAttribute 注解的使用

> 原文：[`c.biancheng.net/view/4412.html`](http://c.biancheng.net/view/4412.html)

通过 org.springframework.web.bind.annotation.ModelAttribute 注解类型可经常实现以下两个功能：

#### 1）绑定请求参数到实体对象（表单的命令对象）

```

@RequestMapping("/register")
public String register(@ModelAttribute("user") UserForm user) {
    if ("zhangsan".equals(uname) && "123456".equals(upass)) {
        logger.info("成功");
        return "login";
    } else {
        logger.info("失败");
        return "register";
}
```

在上述代码中“@ModelAttribute（"user"）UserForm user”语句的功能有两个：

*   将请求参数的输入封装到 user 对象中。
*   创建 UserForm 实例。

以“user”为键值存储在 Model 对象中，和“model.addAttribute（"user"，user）”语句的功能一样。如果没有指定键值，即“@ModelAttribute UserForm user”，那么在创建 UserForm 实例时以“userForm”为键值存储在 Model 对象中，和“model.addAtttribute（"userForm", user）”语句的功能一样。

#### 2）注解一个非请求处理方法

被 @ModelAttribute 注解的方法将在每次调用该控制器类的请求处理方法前被调用。这种特性可以用来控制登录权限，当然控制登录权限的方法有很多，例如拦截器、过滤器等。

使用该特性控制登录权限，创建 BaseController，代码如下所示：

```

package controller;

import javax.servlet.http.HttpSession;

import org.springframework.web.bind.annotation.ModelAttribute;

public class BaseController {
    @ModelAttribute
    public void isLogin(HttpSession session) throws Exception {
        if (session.getAttribute("user") == null) {
            throw new Exception("没有权限");
        }
    }
}
```

创建 ModelAttributeController ，代码如下所示：

```

package controller;

import org.springframework.web.bind.annotation.RequestMapping;

@RequestMapping("/admin")
public class ModelAttributeController {

    @RequestMapping("/add")
    public String add() {

        return "addSuccess";

    }

    @RequestMapping("/update")
    public String update() {

        return "updateSuccess";

    }

    @RequestMapping("/delete")
    public String delete() {

        return "deleteSuccess";
    }
}
```

在上述 ModelAttributeController 类中的 add、update、delete 请求处理方法执行时，首先执行父类 BaseController 中的 isLogin 方法判断登录权限，可以通过地址“http://localhost：8080/springMVCDemo02/admin/add”测试登录权限。