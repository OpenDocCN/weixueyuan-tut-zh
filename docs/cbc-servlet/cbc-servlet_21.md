# Servlet 事件监听器是什么？

> 原文：[`c.biancheng.net/view/4044.html`](http://c.biancheng.net/view/4044.html)

Servlet 事件监听器是一个实现了特定接口的 Java 程序，这个程序专门用于监听 Web 应用中 ServletContext、HttpSession 和 ServletRequest 等域对象的创建和销毁过程、监听这些域对象属性的修改以及感知绑定到 HttpSession 域中的某个对象的状态。

Servlet 规范中定义了八种监听器，这八种监听器的类型及作用如表 1 所示。

在表 1 中，HttpSessionActivationListener 的描述中涉及活化和钝化的概念，HttpSession 对象从内存中转移至硬盘的过程称为钝化，HttpSession 对象从持久化状态变为运行状态的过程称为活化。

表 1 Servlet 事件监听器

| 类   型 | 描   述 |
| --- | --- |
| ServletContextListener | 用于监听 ServletContext 对象的创建与销毁过程 |
| HttpSessionListener | 用于监听 HttpSession 对象的创建和销毁过程 |
| ServletRequestListener | 用于监听 ServletRequest 对象的创建和销毁过程 |
| ServletContextAttributeListener | 用于监听 ServletContext 对象中的属性变更 |
| HttpSessionAttributeListener | 用于监听 HttpSession 对象中的属性变更 |
| ServletRequestAttributeListener | 用于监听 ServletRequest 对象中的属性变更 |
| HttpSessionBindingListener | 用于监听 JavaBean 对象绑定到 HttpSession 对象和从 HttpSession 对象解绑的事件 |
| HttpSessionActivationListener | 用于监听 HttpSession 中对象活化和钝化的过程 |

根据监听事件的不同，可以将表中的监听器分为如下三类。

1）用于监听域对象创建和销毁的事件监听器（ServletContextListener 接口、HttpSessionListener 接口、ServletRequestListener 接口）。

2）用于监听域对象属性增加和删除的事件监听器（ServletContextAttributeListener 接口、HttpSessionAttributeListener 接口、ServletRequestAttributeListener 接口）。

3）用于监听绑定到 HttpSession 域中某个对象状态的事件监听器（HttpSessionBindingListener 接口、HttpSessionActivationListener 接口）。

在 Servlet 规范中，这三类事件监听器都定义了相应的接口，在编写事件监听器程序时只需实现对应的接口即可。在使用监听程序时，Web 服务器会根据监听器所实现的接口，把它注册到被监听的对象上，当触发了某个对象的监听事件时，Web 容器将会调用 Servlet 监听器与之相关的方法对事件进行处理。