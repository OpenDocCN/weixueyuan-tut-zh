# Nginx CGI、FastCGI、SCGI、WSGI 网关接口简介

> 原文：[`www.weixueyuan.net/a/720.html`](http://www.weixueyuan.net/a/720.html)

我们将服务端程序分为了 web 服务器和应用程序服务器两个部分。web 服务器是用于处理 HTML 文件，让客户可以通过浏览器进行访问。主流的有 Apache、IIS、Nginx、Lghttpd 等。而应用服务器处理业务逻辑，比如使用 python 的 django,flask 写成的程序。

通常来自客户端浏览器的请求被 web 服务器截获，如果是静态请求，则如 nginx 会自己做处理，如果是动态请求，则会抛给后端应用服务器来处理。于是如何在 web 服务器与应用服务器之间进行通信成了主要问题，这就引出了以下四种处理的接口：CGI、FastCGI、SCGI、WSGI。

#### 1、CGI（Common Gateway Interface，通用网关接口）

CGI 是一种通用网关接口规范，该规范详细描述了 Web 服务器和请求处理程序（脚本解析器）在获取及返回数据过程中传输数据的标准，如 HTTP 协议的参数名称等。大多数 Web 程序以脚本形式接收并处理请求，然后返回响应数据，如脚本程序 PHP、JSP、Python 等。

#### 2、FastCGI（Fast Common Gateway Interface，快速通用网关接口）

FastCGI 是 CGI 的增强版本，其将请求处理程序独立于 Web 服务器之外，并通过减少系统为创建进程而产生的系统开销，使 Web 服务器可以处理更多的 Web 请求。FastCGI 与 CGI 的区别在于，FastCGI 不像 CGI 那样对 Web 服务器的每个请求均建立一个进程进行请求处理，而是由 FastCGI 服务进程接收 Web 服务器的请求后，由自己的进程自行创建线程完成请求处理。

#### 3、SCGI（Simple Common Gateway Interface，简单通用网关接口）

SCGI 是 CGI 的替代版本，它与 FastCGI 类似，同样是将请求处理程序独立于 Web 服务器之外，但更容易实现，性能比 FastCGI 要弱一些。

#### 4、WSGI（Web Server Gateway Interface，Web 服务网关接口）

WSGI 是为 Python 语言中定义的 Web 服务器与 Python 应用程序或框架间的通用通信接口，可以使 Python 应用程序或框架与支持这一协议的不同 Web 服务器进行通信。常见的 Python Web 框架都实现了该协议的封装。