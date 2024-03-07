# Android HTTP 通信

> 原文：[`c.biancheng.net/view/3144.html`](http://c.biancheng.net/view/3144.html)

Android 系统提供的网络编程方式基于 Java 语言，Java 语言提供的网络编程方式在 Andriod 中都提供了支持。

具体的编程方式包括：针对 TCP/IP 协议的 Socket、ServerSocket 编程方式，针对 UDP 协议的 DatagramSocket、DatagramPackage 编程方式，针对直接网络 URL 访问的 URL、URLConnection 和 HttpURLConnection 方式，等等。首先我们来讲 HTTP 通信编程方式进行讲解。

HTTP 英文全称为 Hyper Text Transfer Protocol，即超文本传输协议，是一种详细规定了浏览器和万维网（World Wide Web，WWW）服务器之间互相通信的规则，通过因特网传送万维网文档的数据传送协议。

HTTP 协议采用请求/响应（Request/Response）模式，该工作模式单向、同步。在客户端向服务器发送请求之后，服务器返回结果之前，客户端只能等待。

客户端向服务器发送一个请求，请求头包含请求的方法、URI、协议版本以及包含请求修饰符、客户信息和内容的类似于 MIME 的消息结构。

服务器以一个状态行作为响应，响应的内容包括消息协议的版本、成功或者错误编码，还包含服务器信息、实体元信息以及可能的实体内容。它是一个属于应用层的面向对象的协议。

由于其简洁、快速，因此适用于分布式超媒体信息系统。在 Internet 上，HTTP 通信通常发生在 TCP/IP 连接之上，缺省端口是 80，但其他的端口也是可用的。

Android 是一种以 Linux 为基础的开放源码操作系统，在其内部包含一些用于实现 Android 网络数据操作的接口。

Android 操作系统提供 3 种网络接口可供使用，它们分别是 Java 标准接口、Apache 接口和 Android 网络接口。其中，Java 标准接口是最常用的，而 Android 接口是 Java 标准接口的补充。

接下来，我们将分别学习这些接口，分析并使用这些接口实现简单的网络操作。需要说明的是，在 Android 系统中开发 Internet 应用程序时，需要在 AndroidManifest.xml 文件中加入如下权限：

<uses-permission android:name="android.permission.INTERNET" />

#### 1\. 标准 Java 接口

Android 提供了 java.net.* 包来实现访问 HTTP 服务的基本功能，其中包含一些非常实用的与网络操作相关的接口，包括流和数据包套接字、Internet 协议、常规 HTTP 处理等。

HTTP 协议通过 URL（Uniform / Universal Resource Locator，统一资源定位符，也被称为网页地址）来定位资源。

URL 是因特网上标准的资源的地址（Address）。

URL 是用于完整地描述 Internet 上网页和其他资源的地址的一种标识方法。这种地址可以是本地磁盘，也可以是局域网上的某一台计算机，更多的是 Internet 上的站点。

URL 由三部分组成：资源类型、存放资源的主机域名、资源文件名。

URL 的一般语法格式为（带方括号“[]”的为可选项）：

protocol :// hostname[:port] / path / [;parameters][?query]#fragment

其中的格式说明如下：

#### 1）protocol（协议）

protocol 指定使用的传输协议，下表列出了常用的 protocol 属性的有效方案名称。最常用的是 HTTP 协议，它也是目前 WWW 中应用最广的协议。

| 方案名称 | 描述内容 |
| --- | --- |
| file | 资源是本地计算机上的文件，格式：file:// |
| ftp | 通过 FTP 访问资源，格式：FTP:// |
| gopher | 通过 Gopher 协议访问该资源，格式：gopher:// |
| http | 通过 HHTP 协议访问该资源，格式：HTTP:// |
| https | 通过安全的 HTTPS 访问该资源，格式：target=_blank>HTTPS:// |
| mailto | 资源为电子邮件地址，通过 SMTP 访问，格式：moilto: |
| MMS | 通过支持 MMS（流媒体）协议播放该资源（代表软件：Windows Media Player），格式 MMS:// |
| ed2k | 通过支持 ed2k（专用下载链接）协议 P2P 软件访问该资源（代表软件：电驴），格式 ed2k:// |
| Flashget | 通过支持 Flashget:（专用下载链接）协议 P2P 软件访问该资源（代表软件：快车），格式 Flashget:// |
| thunder | 通过支持 thunder（专用下载链接）协议 P2P 软件访问该资源（代表软件：迅雷），格式 thunder:// |
| news | 通过 NNTP 访问该资源，格式：news:// |

#### 2) hostname（主机名）

hostname 是指存放资源的服务器的域名系统（DNS）、主机名或 IP 地址。有时在主机名前也可以包含连接到服务器所需的用户名和密码（格式：username@password）。

#### 3）port（端口号）

port 为整数，是可选的，省略时使用方案的默认端口，各种传输协议都有默认的端口号，如 HTTP 的默认端口为 80。

若输入时省略，则使用默认端口号。有时出于安全或其他因素考虑，可以在服务器上对端口进行重定义，即采用非标准端口号，此时，URL 中就不能省略端口号这一项。

#### 4）path（路径）

path 是由若干“/”符号隔开的字符串，一般用来表示主机上的一个目录或文件地址。

#### 5）parameters（参数）

parameters 用于指定特殊参数的可选项。

#### 6）query（查询）

query 也是可选项，用于给动态网页（如使用 CGI、ISAPI、PHP/JSP/ASP/ASP.NET 等技术制作的网页）传递参数，可有多个参数，用“&”符号隔开，每个参数的名和值用“=”符号隔开。

#### 7）fragment（信息片断）

fragment 是字符串，用于指定网络资源中的片断。例如一个网页中有多个名词解释，可使用 fragment 直接定位到某一名词解释。

使用 Java 标准接口访问网络资源的基本步骤如下：

1.  创建 URL。
2.  从 URL 创建 URLConnection /HttpURLConnection 对象并设置连接参数。
3.  连接到服务器。
4.  读写服务器数据。

Java.net.URL 类用于封装 URL 地址，可以通过该类与特定 URL 地址建立连接并对其中的数据进行读写操作。若封装的 URL 地址格式错误，则 URL 构造方法会抛出 MalformedURLException 异常。

#### 2\. Apache 接口

Apache 实验室开源的包 org.apache.http.* 提供非常丰富的网络操作接口。弥补了 java.net.* 灵活性不足的缺点，对 java.net.* 进行封装，功能更加强大和全面，也会给 Android 带来更加丰富多彩的网络应用。

在 Apache 网络接口中，最重要的是 HttpClient，HttpClient 是 Apache Jakarta Common 下的子项目，可以用来提供高效的、最新的、功能丰富的、支持 HTTP 协议的客户端编程工具包，并且它支持 HTTP 协议最新的版本和建议。

HttpClient 已经应用在很多项目中，比如 Apache Jakarta 上很著名的另外两个开源项目 Cactus 和 HTMLUnit 都使用了 HttpClient。它是一个开源项目，功能更加完善，为客户端的 HTTP 编程提供高效、最新、功能丰富的工具包支持。

Android 平台引入了 Apache HttpClient 的同时还提供了对它的一些封装和扩展，例如设置缺省的 HTTP 超时和缓存大小等。

Android 使用的是目前最新的 HttpClient 4.0（org.apache.http.*），可以将 Apache 视为目前流行的开源 Web 服务器，主要包括创建 HttpClient 以及 Get/Post、HttpRequest 等对象、设置连接参数、执行 HTTP 操作、处理服务器返回结果等功能。

使用这部分接口的基本操作与 java.net.* 基本类似，主要包括：

*   创建 HttpClient，以及 GetMethod / PostMethod 和 HttpRequest 等对象。
*   设置连接参数。
*   执行 HTTP 操作。
*   处理服务器返回结果。

以下列出的是 HttpClient 提供的主要功能。

*   实现了所有 HTTP 的方法（GET、POST、PUT、HEAD 等）。
*   支持自动转向。
*   支持 HTTPS 协议。
*   支持代理服务器。

使用 HttpClient 时也需要注意请求报头和响应报头，以及提交方式，因为它也是遵循 HTTP 协议的。下面简单介绍一下常用的 GET 和 POST 方式在代码实现上有什么异同。

在 GET 方式下使用 HttpClient 需要几个最基本的步骤：

1.  构造 HttpClient 的实例。
2.  创建连接方法的实例，这里是 HttpGet，在 HttpGet 的构造方法中传入待连接的路径。
3.  请求 HttpClient，调用 execute 传入 HttPGet 取得 HttpResponse。
4.  读 HttpResponse，在读之前判断连接状态是否等于 HttpStatus.SC_OK(200)。
5.  对读取的内容进行处理。

Post 方式相对 GET 有些差异并且复杂一点，主要是参数处理部分有差异。在 POST 方式下使用 HttpClient 需要几个最基本的步骤：

1.  构造 HttpClient 的实例。
2.  向 HttpPost 的构造参数中传入路径，创建 POST 连接。
3.  准备参数，并且设置编码等相关信息。
4.  将准备的参数设置到 HttpPost 中去，方法是 HttpPost.setEntity()。
5.  得到 HttpResponse，通过 httpClient.execute() 得到。
6.  读取 HttpResponse。
7.  对读取的内容进行处理。

需要注意的是，在网络操作过程中，需要 Android 应用拥有联网权限，可以在 AndroidManifest.xml 中写入 <uses-permission android:name="android.permission.INTERNET"/> 权限。

#### 3．Android 网络接口

android.net.* 包实际上是通过对 Apache 中 HttpClient 的封装来实现的一个 HTTP 编程接口，同时还提供 HTTP 请求队列管理以及 HTTP 连接池管理，以提高并发送请求情况下的处理效率，

除此之外，还有网络状态监视等接口、网络访问的 Socket、常用的 Uri 类以及有关 WiFi 相关的类等。