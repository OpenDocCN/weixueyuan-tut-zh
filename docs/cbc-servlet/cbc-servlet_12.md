# HttpServletResponse 详解：封装 HTTP 响应消息

> 原文：[`c.biancheng.net/view/4014.html`](http://c.biancheng.net/view/4014.html)

HttpServletResponse 接口继承自 ServletResponse 接口，主要用于封装 HTTP 响应消息。由于 HTTP 响应消息分为状态行、响应消息头、消息体三部分。因此，在 HttpServletResponse 接口中定义了向客户端发送响应状态码、响应消息头、响应消息体的方法，本节将针对这些方法进行详细讲解。

## 发送状态码相关的方法

当 Servlet 向客户端回送响应消息时，需要在响应消息中设置状态码。因此，HttpServletResponse 接口定义了两个发送状态码的方法。

#### 1）setStatus（int status）方法

该方法用于设置 HTTP 响应消息的状态码，并生成响应状态行。由于响应状态行中的状态描述信息直接与状态码相关，而 HTTP 版本由服务器确定，因此，只要通过 setStatus（int status）方法设置了状态码，即可实现状态行的发送。需要注意的是，在正常情况下，Web 服务器会默认产生一个状态码为 200 的状态行。

#### 2）sendError（int sc）方法

该方法用于发送表示错误信息的状态码。例如，404 状态码表示找不到客户端请求的资源。response 对象提供了两个重载的 sendError（int sc）方法，具体如下：

public void sendError(int code) throws java.io.IOException
public void sendError(int code,String message)throws java.io.IOException

在上面重载的两个方法中，第一个方法只发送错误信息的状态码，而第二个方法除了发送状态码以外，还可以增加一条用于提示说明的文本信息，该文本信息将出现在发送给客户端的正文内容中。

## 发送响应消息头相关的方法

Servlet 向客户端发送的响应消息中包含响应头字段，由于 HTTP 协议的响应头字段有很多种，因此，HttpServletResponse 接口定义了一系列设置 HTTP 响应头字段的方法，如表 2 所示。

表 1 设置响应消息头字段的方法

| 方法声明 | 功能描述 |
| --- | --- |
| void addHeader(String name,String value) | 这两个方法都是用于设置 HTTP 协议的响应头字段。其中，参数 name 用于指定响应头字段的名称，参数 value 用于指定响 应头字段的值。不同的是，addHeader() 方法可以增加同名的响应头字段，而 setHeader() 方法则会覆盖同名的头字段 |
| void setHeader (String name,String value) |
| void addIntHeader(String name,int value) | 这两个方法专门用于设置包含整数值的响应头，避免了使用 addHeader() 与 setHeader() 方法时需要将 int 类型的设置值转换为 String 类型的麻烦 |
|  void setIntHeader(String name, int value) |
| void setContentType(String type) | 该方法用于设置 Servlet 输出内容的 MIME 类型，对于 HTTP 协议来说，就是设置 Content-Type 响应头字段的值。例如，如果发送到客户端的内容是 jpeg 格式的图像数据,就需要将响应头字段的类型设置为 image/jpeg。需要注意的是，如果响应的内容为文本，setContentType() 方法还可以设置字符编码，如 text/html;charset = UTF-8 |
| void setLocale (Locale loc) | 该方法用于设置响应消息的本地化信息。对 HTTP 来说，就是设置 Content-Language 响应头字段和 Content-Type 头字段中的字符集编码部分。需要注意的是，如果 HTTP 消息没有设置 Content-Type 头字段，则 setLocale() 方法设置的字符集编码不会出现在 HTTP 消息的响应头中，如果调用 setCharacterEncoding() 或 setContentType() 方法指定了响应内 容的字符集编码，则 setLocale() 方法将不再具有指定字符集编码的功能 |
| void setCharacterEncoding(String charset) | 该方法用于设置输出内容使用的字符编码，对 HTTP 协议来说，就是设置 Content-Type 头字段中的字符集编码部分。如果没有设置 Content-Type 头字段，则 setCharacterEncoding 方法设 置的字符集编码不会出现在 HTTP 消息的响应头中。setCharacterEncoding() 方法比 setContentType() 和 setLocale() 方法的优先权高，它的设置结果将覆盖 setContentType() 和 setLocale() 方法所设置的字符码表 |

需要注意的是，在表 1 列举的一系列方法中，addHeader()、setHeader()、addIntHeader()、setIntHeader() 方法都用于设置各种头字段，而 setContetType()、setLoacale() 和 setCharacterEncoding() 方法用于设置字符编码，这些设置字符编码的方法可以有效解决乱码问题。

## 发送响应消息体相关的方法

由于在 HTTP 响应消息中，大量的数据都是通过响应消息体传递的，因此，ServletResponse 遵循以 I/O 流传递大量数据的设计理念。在发送响应消息体时，定义了两个与输出流相关的方法。

#### 1）getOutputStream() 方法

该方法所获取的字节输出流对象为 ServletOutputStream 类型。由于 ServletOutputStream 是 OutputStream 的子类，它可以直接输出字节数组中的二进制数据。因此，要想输出二进制格式的响应正文，就需要使用 getOutputStream() 方法。

#### 2）getWriter() 方法

该方法所获取的字符输出流对象为 PrintWriter 类型。由于 PrintWriter 类型的对象可以直接输出字符文本内容，因此，要想输出内容全部为字符文本的网页文档，则需要使用 getWriter() 方法。

注意：虽然 response 对象的 getOutputStream() 和 getWriter() 方法都可以发送响应消息体，但是，它们之间互相排斥，不可同时使用，否则会发生 IllegalStateException 异常。