# 与 Servlet 相关的接口和类

> 原文：[`c.biancheng.net/view/3982.html`](http://c.biancheng.net/view/3982.html)

Sun 公司提供了一系列的接口和类用于 Servlet 技术的开发，其中最重要的接口是 javax.servlet.Servlet。在 Servlet 接口中定义了 5 个抽象方法，如表 1 所示。

表 1 Servlet 接口的抽象方法

| 方法声明 | 功能描述 |
| --- | --- |
| void init(ServletConfig config) | 容器在创建好 Servlet 对象后，就会调用此方法。该方法接收一个 ServletConfig 类型的参数，Servlet 容器通过该参数向 Servlet 传递初始化配置信息 |
| ServletConfig getSendetConfig() | 用于获取 Servlet 对象的配置信息，返回 Servlet 的 ServletConfig 对象 |
| String getServletInfo() | 返回一个字符串，其中包含关于 Servlet 的信息，如作者、版本和版权等信息 |
| voidservice (ServletRequest request,ServletResponse response) | 负责响应用户的请求，当容器接收到客户端访问 Servlet 对象的请求时，就会调用此方法。 容器会构造一个表示客户端请求信息的 ServletRequest 对象和一个用于响应客户端的 ServletResponse 对象作为参数传递给 service() 方法。
在 service() 方法中，可以通过 ServletRequest 对象得到客户端的相关信息和请求信息，在对请求进行处理后，调用 ServletResponse 对象的方法设置响应信息 |
| void destroy() | 负责释放 Servlet 对象占用的资源。当服务器关闭或者 Servlet 对象被移除时，Servlet 对象会被销毁，容器会调用此方法 |

在表 1 中，列举了 Servlet 接口中的五个方法，其中 init()、service() 和 destroy() 方法可以表现 Servlet 的生命周期，它们会在某个特定的时刻被调用。

针对 Servlet 的接口，Sun 公司提供了两个默认的接口实现类：GenericServlet 和 HttpServlet。其中，GenericServlet 是一个抽象类，该类为 Servlet 接口提供了部分实现，它并没有实现 HTTP 请求处理。

HttpServlet 是 GenericServlet 的子类，它继承了 GenericServlet 的所有方法，并且为 HTTP 请求中的 GET 和 POST 等类型提供了具体的操作方法。通常情况下，编写的 Servlet 类都继承自 HttpServlet，在开发中使用的也是 HttpServlet 对象。

HttpServlet 类中包含两个常用方法，这两个方法的说明如表 2 所示。

表 2 HttpServlet 类的常用方法

| 方法声明 | 功能描述 |
| --- | --- |
| protected void doGet (HttpServletRequest req, HttpServletResponse resp) | 用于处理 GET 类型的 HTTP 请求的方法 |
| protected void doPost(HttpServletRequest req, HttpServletResponse resp) | 用于处理 POST 类型的 HTTP 请求的方法 |

HttpServlet 主要有两大功能，具体如下。

1.  根据用户请求方式的不同，定义相应的 doXxx() 方法处理用户请求。例如，与 GET 请求方式对应的 doGet() 方法，与 POST 方式对应的 doPost() 方法。
2.  通过 service() 方法将 HTTP 请求和响应分别强转为 HttpServletRequest 和 HttpServletResponse 类型的对象。

需要注意的是，由于 HttpServlet 类在重写的 service() 方法中，为每一种 HTTP 请求方式都定义了对应的 doXxx() 方法，因此，当定义的类继承 HttpServlet 后，只需要根据请求方式重写对应的 doXxx() 方法即可，而不需要重写 service() 方法。