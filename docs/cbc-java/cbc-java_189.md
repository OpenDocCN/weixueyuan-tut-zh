# Java 什么是 URL？及 URL 类和 URLConnection 类

在 Java 的 API 中的 java.net 包中包含一个 URL 类和一个 URLConnection 类。下面介绍这两个类的相关知识。

## URL 概念

URL 是统一资源定位符（Uniform Resource Locator）的简称，它表示 Internet 上某一资源的地址。通过 URL 用户可以访问各种网络资源，比如常见的 WWW 以及 FTP 站点。浏览器可以通过解析给定的 URL 在网络上查找相应的文件或其他资源。

URL 的语法格式如下所示。

```
protocol://resourceName
```

协议名（protocol）指明获取资源所使用的传输协议，如 HTTP、FTP 和 file 等，资源名（resourceName）则应该是资源的完整地址，包括主机名、端口号、文件名或文件内部的一个引用。下面是一些简单的 URL 示例。

```
http://www.sun.com/    协议名：//主机名
http://localhost:8080/Test/admin/login.jsp 协议名://机器名：端口号/文件名
```

## URL 类

在 java.net 包中包含专门用来处理 URL 的类 URL，可以获得 URL 的相关信息，例如 URL 的协议名和主机名等。下面分别对它的构造方法和常用方法进行介绍。

URL 的构造方法如表 1 所示。

表 1 URL 的构造方法

| 构造方法 | 说明 |
| public URL (String spec) | 通过一个表示 URL 地址的字符串可以构造一个 URL 对象。 |
| public URL(URL context,String spec) | 使用基本地址和相对 URL 构造一个 URL 对象。 |
| public URL(String protocol,String host,String file) | 使用指定的协议、主机名和文件名创建一个 URL 对象。 |
| public URL(String protocol,String host,int port,String file) | 使用指定的协议、主机名、端口号和文件名创建一个 URL 对象。 |

URL 的常用方法如表 2 所示。

表 2 URL 的常用方法

| 方法 | 说明 |
| public String getProtocol() | 获取该 URL 的协议名。 |
| public String getHost() | 获取该 URL 的主机名。 |
| public int getPort() | 获取该 URL 的端口号，如果没有设置端口，返回 -1。 |
| public String getFile() | 获取该 URL 的文件名。 |
| public String getRef() | 获取该 URL 在文件中的相对位置。 |
| public String getQuery() | 获取该 URL 的查询信息。 |
| public String getPath() | 获取该 URL 的路径。 |
| public String getAuthority() | 获取该 URL 的权限信息。 |
| public String getUserInfo() | 获得使用者的信息。 |
| public String getRef() | 获得该 URL 的锚点。 |

## URLConnection 类

完成了 URL 的定义，接下来就可以获得 URL 的通信连接。在 java.net 包中，定义了专门的 URLConnection 类来表示与 URL 建立的通信连接，URLConnection 类的对象使用 URL 类的 openConnection() 方法获得。

URLConnection 类的主要方法如表 3 所示。

表 3 URLConnection 类的主要方法

| 方法 | 说明 |
| void addRequestProperty(String key,String value) | 添加由键值对指定的一般请求属性。key 指的是用于识别请求的关键字 （例如 accept），value 指的是与该键关联的值。 |
| void connect() | 打开到此 URL 所引用的资源的通信链接（如果尚未建立这样的链接）。 |
| Object getConnection() | 检索此 URL 链接的内容。 |
| InputStream getInputStream() | 返回从此打开的链接读取的输入流。 |
| OutputStream getOutputStream() | 返回写入到此链接的输出流。 |
| URL getURL() | 返回此 URLConnection 的 URL 字段的值。 |

#### 例 1

使用 URL 和 URLConnection 类获取与百度首页的链接并将其页面信息输出到控制台，主要步骤如下所示。

(1) 创建一个类，编写 main() 方法，在该方法中创建一个 URL 对象，然后传入参数“http://www.baidu.com/”，输出 URL 的相关信息，代码如下所示。

```
package ch16;
import java.io.IOException;
import java.io.InputStream;
import java.net.URL;
import java.net.URLConnection;
public class URLDemo
{
    public static void main(String[] args)
    {
        try
        {
            URL url=new URL("http://www.baidu.com/");
            System.out.println("协议：" + url.getProtocol());
            System.out.println("主机：" + url.getHost());
            System.out.println("端口：" + url.getPort());
            InputStream in;
        }
        catch(IOException e)
        {
            //TODO 自动生成的 catch 块
            e.printStackTrace();
        }
    }
}
```

(2) 在 main() 方法的 try 模块中继续添加代码，获得 URLConnection 对象，通过输入流读取页面源代码并将信息输出到控制台，代码如下所示。

```
URLConnection uc=url.openConnection();
in=uc.getInputStream();
byte[] b=new byte[1024];
int len;
while((len=in.read(b))!=-1)
{
    System.out.println(new String(b,0,len));
}
in.close();
```

(3) 运行程序，执行结果如下所示。

```
协议：http
主机：www.baidu.com
端口：-1
<!DOCTYPE html>
<!--STATUS OK--><html> <head><meta http-equiv=content-type content=text/html;charset=utf-8><meta http-equiv=X-UA-Compatible content=IE=Edge><meta content=always name=referrer><link rel=stylesheet type=text/css href=http://s1.bdstatic.com/r/www/cache/......<img src=//www.baidu.com/img/gs.gif> </p> </div> </div> </div> </body> </html>
```

## 提取 URL 协议名称

我们知道 URL 中必须有一个协议名称，常用的协议有 HTTP、HTTPS 和 FTP 等。本实例将允许用户输入一个 URL，然后从中提取出协议名称。

实例代码如下：

```
package ch16;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.Scanner;
public class URLDemo1
{
    public static void main(String[] args)
    {
        try
        {
            Scanner scan=new Scanner(System.in);    //创建输入扫描器
            System.out.println("请输入一个完整的网址：");
            String line=scan.nextLine();    //获取用户输入文本
            URL url=new URL(line);    //创建 URL 对象
            System.out.println("这个网址的主机名称是："+url.getHost());    //获取主机名称
            System.out.println("这个网址的 URL 协议名称是："+url.getProtocol());    //获取协议名称
        }
        catch(MalformedURLException e)
        {
            System.out.println("输入的是非法网址");    //提示错误信息
        }
    }
}
```

如上述代码所示，在创建一个 URL 类对象之后调用 getHost() 方法获取主机名称，调用 getPmtocol() 方法获取协议名称。实例运行结果如下所示。

```
请输入一个完整的网址：
http://www.baidu.com
这个网址的主机名称是：www.baidu.com
这个网址的 URL 协议名称是：http
```

```
请输入一个完整的网址：
ftp://www.baidu.com/seo
这个网址的主机名称是：www.baidu.com
这个网址的 URL 协议名称是：ftp
```