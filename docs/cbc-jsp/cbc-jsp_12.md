# JSP 常见错误以及解决方案

本节我们分析一下常见的 JSP 错误信息，并给出解决方案。这些错误大家在实际开发中会经常遇到，所以有必要在此提及一下。

(1) 页面显示 500 错误，错误信息如下：

```
An error occurred at line: 6 in the generated java file
Syntax error on token ";", import expected after this token
```

错误原因见如下代码：

```
<%@ page langue="java" import="java.utli.*; java.text,*"
pageEncoding="GBK">
```

import 中的分隔符应该是逗号，不能用分号。

(2) 页面显示 500 错误，错误信息如下：

```
org.apache.jasper.JasperException: Unable to compile class for JSP:
An error occurred at line: 6 in the generated Java file
Syntax error on tokens, delete these tokens
```

此类信息都表示页面的编写出现了语法错误。

例如，指令中出现了错误字符，或者使用了错误的属性名，或者有错误的属性值。

(3) 页面显示 500 错误，错误信息如下：

```
org.apache.jasper.JasperException: /index.jsp(1,1) Unterminated &lt;
%@ page tag
```

该信息告诉用户：指令标签有错误。

(4) 页面显示中文为乱码。例如：

```
???????JSP??---?????
```

原因见如下代码：

```
<%@ page language="java" contentType="text/html,charset=GBK" import="java.util.*,java.text.*" pageEncoding="GBK"%>
```

这里`contentType="text/html,charset=GBK"`分隔符用的是逗号，而此处只能用分号。

(5) 错误：ClassNotFoundException。代表类没有被找到的异常。

原因：通常出现在 JDBC 连接代码中，对应的驱动 JAR 包没有导入，或 sqljdbc.jar 对应的 Class.forName(类名) 中的类名写错了。

(6) 错误信息：主机 TCP/IP 连接失败。

原因：SQL Server 配置管理器中，未启用对应的 SQL Server 服务的 TCP/IP 协议；或 SQL Server 服务器没有开启服务；或连接字符串中的 localhost 写错了；或启用的服务是开发版的 SQL Server，即启用了 SQL Express 服务；或端口号写成了 localhost:8080。

(7) 出错信息：数据库连接失败。

*   检查 JAR 包导入。
*   检查连接字符串和驱动类字符串（要避免使用 SQL Server 2000 的连接字符串），例如“databasename=数据库名”写成了“datebasename=数据库名”或“localhost: 1433”写成了“localliost:8080”。