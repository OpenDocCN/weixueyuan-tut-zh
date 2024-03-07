# JSP config 对象

config 对象是 javax.servlet.ServletConfig 类的实例，表示 Servlet 的配置信息。

当一个 Servlet 初始化时，容器把某些信息通过此对象传递给这个 Servlet，这些信息包括 Servlet 初始化时所要用到的参数（通过属性名和属性值构成）以及服务器的有关信息（通过传递一个 ServletContext 对象），config 对象的应用范围是本页。

开发者可以在 web.xml 文件中为应用程序环境中的 Servlet 程序和 JSP 页面提供初始化参数。表 1 列出了 config 对象的常用方法。

表 1 config 对象的常用方法

| 方法 | 说明 |
| ServletContext getServletContext() | 返回所执行的 Servlet 的环境对象 |
| String getServletName() | 返回所执行的 Servlet 的名字 |
| String getInitParameter(String name) | 返回指定名字的初始参数值 |
| Enumeration getlnitParameterName() | 返回该 JSP 中所有初始参数名，一个枚举 |