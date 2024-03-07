# Servlet 是什么？

在 JSP 中，Servlet 是另一种重要的技术，全称是 Java Servlet，主要用于 Java 类编写的服务端程序，与平台架构、协议无关。在 JSP 中，所有的 JSP 页面传回服务端时都要转为 Servlet 进行编译、运行。

## Servlet 的定义

Servlet 是 Java 专注于 CGI 开发的一种技术，运行在 Server 端，并产生动态的结果。为什么要使用 Servlet 来代替传统的 CGI 程序呢？

原因之一是效率，使用传统的 CGI 程序，每当收到一个 HTTP 请求的时候，系统就要启动一个新的进程来处理这个请求，这样会导致系统性能降低。而使用 Servlet，Java VMS 一直在运行，当接到一个请求之后 Java MVS 就创建一个 Java 线程马上进行处理，如此要比每次都启动一个新的系统进程效率要高得多。

JSP 是 Seivlet 技术的一个扩展。JSP 允许将 Java 代码轻松地和 HTML 语言混合在一起使用，并完成强大的功能。可以使代码更容易阅读并在浏览器中浏览到程序执行的结果。

【例 1】下面是一个例子，输出的结果都是“Hello World! Your name is:”，请仔细比较。

JSP 文件代码如下：

```
<%@ page contentType="text/html;charset=utf-8" language="java" import="java.sql.*" errorPage="errorpage.jsp" %>
<html>
<head>
    <title>JSP</title>
</head>
<head>
<%
    out.println("Hello World! Your name is: "+request.getParameter("name"));
%>
</body>
</html>
```

相应的 Servlet 文件代码如下：

```
import java.io.*;
import javax.servlet.*;
import javax.servlet.http.*;
public class HelloWorld extends HttpServlet
{
    public void doGet(HttpServletRequest request,HttpServletResponse response) throws IOException,ServletException
    {
        response.setContentType("text/html");
        PrintWriter out=response.getWriter();
        out.println("");
        out.println("");
        out.println("");
        out.println("");
        out.println("");
        out.println("Hello World! Your name is: "+request.getParameter("name"));
        out.println("");
        out.println("");
    }
}
```

上面两个程序的输出结果是完全一样的，从而可以看到，JSP 可以实现 Servlet 的一般功能，其中 JSP 程序显得更容易阅读和编写。JSP 和 Servlet 具有不同的特点，应用的场合也不同，程序员在使用的时候，可以根据自己的需要进行选择。

## Servlet 的技术特点

Servlet 给程序开发带来了许多好处，能及时响应和处理 Web 端请求。总体来说，Servlet 技术有如下几个特点。

1.  功能强大：Servlet 可以提供传统的 CGI 不能提供的许多强大功能。可以使用 Java 的 API 完成任何传统 CGI 认为困难或不可能的事情。Servlet 可以轻松地实现数据共享和信息维护、跟踪 session 和其他功能。
2.  安全：Servlet 运行在 Servlet 引擎的限制范围之内，就像可以在 Web 浏览器中运行 Applets 一样，这样有助于保护 Servlet 不受烕胁。
3.  成本：由于 Servlet 可以运行在多个 Web 服务器上，这样就可以使用免费或价格便宜的服务器，如此可以大大减少成本开支。
4.  灵活性：由于 Servlet 是在 Java 平台上运行的，所以由于 Java 的跨平台性，Servlet 也可以从一个平台轻易地转移到另一个操作系统平台上，从而大大提高了灵活性。
5.  一个 Servlet 实际上就是一个 Java 类，需要运行在 Java 的虚拟机上，使用 Servlet 引擎。当某个 Servlet 被请求的时候，Servlet 引擎调用该 Servlet 并一直运行到这个被调用的 Servlet 运行完毕或 Servlet 引擎被关闭。