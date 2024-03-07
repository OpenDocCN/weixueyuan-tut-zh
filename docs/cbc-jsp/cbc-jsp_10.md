# JSP 表达式

表达式用于在页面中输出信息，其使用格式如下：

```
<%=变量或可以返回值的方法或 Java 表达式%>
```

特别要注意，“<%”与之间不要有空格。

JSP 表达式在页面被转换为 Servlet 后，变成了 outprint() 方法。所以，JSP 表达式与 JSP 页面中嵌入小脚本程序中的 out.print() 方法实现的功能相同。如果通过 JSP 表达式输出一个对象，则该对象的 toString() 方法会被自动调用，表达式将输出 toString() 方法返回的内容。

JSP 表达式可以应用于以下几种情况。

#### 1\. 向页面输出内容

【例 1】向页面输出内容：

```
<% String name = "www.123.com"; %>
用户名：<%=name%>
```

上述代码将生成如下运行结果：

```
用户名：www.123.com
```

#### 2\. 生成动态的链接地址

【例 2】生成动态的链接地址：

```
<% String path = "welcome.jsp"; %>
<a href="<%=path%>">链接到 welcome.jsp</a>
```

上述代码将生成如下的 HTML 代码：

```
<a href="welcome.jsp">链接到 welcome.jsp</a>
```

#### 3\. 动态指定 Form 表单处理页面

【例 3】动态指定 Form 表单处理页面：

```
<% String name = "logon.jsp"; %>
<form action="<%=name%>"></form>
```

上述代码将生成如下 HTML 代码：

```
<form action="logon.jsp"></form>
```

#### 4\. 为通过循环语句生成的元素命名

【例 4】为通过循环语句生成的元素命名：

```
<%
for(int i=1;i<3;i++) {
%>
file<%=i%>:<input type="text" name="<%="file"+i%>"><br>
<%
}
%>
```

上述代码将生成如下 HTML 代码：

```
file1:<input type="text" name="file1"><br>
file2:<input type="text" name="file2"><br>
```