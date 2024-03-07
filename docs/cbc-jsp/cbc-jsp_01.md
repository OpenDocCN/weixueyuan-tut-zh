# JSP 是什么？

JSP 是一种简化的 Servlet 设计，可以调用强大的 Java 类库，并可以与其他相关的一些技术（Servlet、JavaBean、EJB）联合工作。下面介绍什么是 JSP 以及它有哪些特点。

JSP（Java Server Pages）的中文含义是 Java 服务器端语言。其核心技术是 Java 技术，以 Servlet 的形式接受用户的访问和处理数据，在服务器端 JSP 文件会被编译为类文件，其扩展名为 .class。

JSP 是由 Sun Microsystems 公司倡导、多家公司参与一起建立的一种动态网页技术标准，其在动态网页的创建中有着强大而特殊的功能，是一种实现普通静态 HTML 和动态 HTML 混合编码的技术。

在 Sun 正式发布 Java Server Pages 之后，这种新的 Web 应用开发技术很快便引起了人们的关注。Java Server Pages 为创建高度动态的 Web 应用提供了一个独特的开发环境。

Java Server Pages（以下简称为 JSP）是 Java 平台上用于编写包含诸如 HTML、DHTML、XHTML 和 XML 等含有动态生成内容的 Web 页面的应用程序的技术。JSP 技术的功能强大，使用灵活，为创建显示动态 Web 内容的页面提供了一个简捷而快速的方法。

JSP 技术的设计目的是使构造基于 Web 的应用程序更加容易和快捷，而这些应用程序能够与各种 Web 服务器、Web 应用服务器、浏览器和开发工具共同工作。

许多由 CGI 程序生成的页面大部分仍旧是静态 HTML，动态内容只在页面中有限的几个部分出现。但是包括 Servlet 在内的大多数 CGI 技术及其变种，总是通过程序生成整个页面。例如，下面就是一个简单的 JSP 页面：

```
<! DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN">
<html>
<head><title>欢迎访问</title></head>
<body>
<h1>欢迎</h1>
<small>欢迎，
<!--首次访问的用户名字为"New User"-->
<!--
out.println 用来输出内容
-->
<% out.println(Utils.getUserNameFromCookie(request)); %>
要设置账号信息，请点击：
<a href="Account-Settings.html">这里</a></small>
<P>
页面的其余内容：
</body></html>
```