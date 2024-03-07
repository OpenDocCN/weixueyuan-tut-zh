# Spring MVC 应用@Autowired 和@Service 进行依赖注入

> 原文：[`c.biancheng.net/view/4411.html`](http://c.biancheng.net/view/4411.html)

在前面学习的控制器中并没有体现 MVC 的 M 层，这是因为控制器既充当 C 层又充当 M 层。这样设计程序的系统结构很不合理，应该将 M 层从控制器中分离出来。

Spring MVC 框架本身就是一个非常优秀的 MVC 框架，它具有依赖注入的优点，可以通过 org.springframework.beans.factory. annotation.Autowired 注解类型将依赖注入到一个属性（成员变量）或方法，例如：

@Autowired
public UserService userService;

在 Spring MVC 中，为了能被作为依赖注入，类必须使用 org.springframework.stereotype.Service 注解类型注明为 @Service（一个服务）。另外，还需要在配置文件中使用 <context：component-scan base-package=“基本包”/> 元素来扫描依赖基本包。

下面《Spring MVC 获取参数的几种常见方式》中“登录”和“注册”的业务逻辑处理分离出来，使用 Service 层实现。

首先创建 service 包，在该包中创建 UserService 接口和 UserServiceImpl 实现类。

UserService 接口的具体代码如下：

```

package service;
import pojo.UserForm;
public interface UserService {
    boolean login(UserForm user);
    boolean register(UserForm user);
}
```

UserServiceImpl 实现类的具体代码如下：

```

package service;
import org.springframework.stereotype.Service;
import pojo.UserForm;

//注解为一个服务
@Service
public class UserServiceImpl implements UserService {

    public boolean login(UserForm user) {
        if ("zhangsan".equals(user.getUname())
                && "123456".equals(user.getUpass())) {
            return true;
        }
        return false;
    }

    public boolean register(UserForm user) {
        if ("zhangsan".equals(user.getUname())
                && "123456".equals(user.getUpass())) {
            return true;
        }
        return false;
    }
}
```

然后在配置文件中添加一个 <context：component-scan base-package=“基本包”/>元素，具体代码如下：

<context:component-scan base-package="service" />

最后修改控制器类 UserController，具体代码如下：

```

package controller;

import javax.servlet.http.HttpSession;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.RequestMapping;

import pojo.UserForm;
import service.UserService;

import com.sun.org.apache.commons.logging.Log;
import com.sun.org.apache.commons.logging.LogFactory;

@Controller
@RequestMapping("/user")
public class UserController {
    // 得到一个用来记录日志的对象，这样在打印信息的时候能够标记打印的是哪个类的信息
    private static final Log logger = LogFactory.getLog(UserController.class);
    // 将服务依赖注入到属性 userService
    @Autowired
    public UserService userService;

    /**
     * 处理登录
     */
    @RequestMapping("/login")
    public String login(UserForm user, HttpSession session, Model model) {
        if (userService.login(user)) {
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
     * 处理注册
     */
    @RequestMapping("/register")
    public String register(@ModelAttribute("user") UserForm user) {
        if (userService.register(user)) {
            logger.info("成功");
            return "login"; // 注册成功，跳转到 login.jsp
        } else {
            logger.info("失败");
            // 使用@ModelAttribute("user")与 model.addAttribute("user",user)的功能相同
            // 在 register.jsp 页面上可以使用 EL 表达式${user.uname}取出 ModelAttribute 的 uname 值
            return "register"; // 返回 register.jsp
        }
    }
}
```