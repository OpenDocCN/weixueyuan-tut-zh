# JSP include 指令

JSP include 指令用于通知 JSP 引擎在翻译当前 JSP 页面时，将其他文件中的内容合并进当前 JSP 页面转换成的 Servlet 源文件中，这种在源文件级别进行引入的方式，称为静态引入，当前 JSP 页面与静态引入的文件紧密结合为一个 Servlet。这些文件可以是 JSP 页面、HTML 页面、文本文件或是一段 Java 代码。其语法格式如下：

<%@ include file="relativeURL|absoluteURL" %>

说明如下：

(1) file 属性指定被包含的文件，不支持任何表达式，例如下面是错误的用法：

```
<% String f="top.html"; %>
<%@ include file="<%=f %>" %>
```

(2) 不可以在 file 所指定的文件后接任何参数，如下用法也是错误的：

```
<%@ include file="top.jsp?name=zyf" %>
```

(3)如果 file 属性值以“/”开头，将在当前应用程序的根目录下查找文件；如果是以文件名或文件夹名开头，将在当前页面所在的目录下查找文件。

#### 提示：

使用 include 指令是以静态方式包含文件，也就是说，被包含文件将原封不动地插入 JSI 文件中，因此，在所包含的文件中不能使用 <html></html>、<body></body> 标记，否则会因为与原有的 JSP 文件有相同标记而产生错误。另外，因为原文件和被包含文件可以相互访问彼此定义的变量和方法，所以要避免变量和方法在命名上产生冲突。

【例 1】使用 include 指令标记静态插入一个文本文件 Hello.txt，并在当前页面同一个 Web 服务目录中显示“很高兴认识你！Nice to meet you.”，具体操作步骤如下。

① Hello.txt 文本文件的代码如下：

```
<%@ page contentType="text/html;charset=utf-8" %>
很高兴认识你!
Nice to meet you.
```

② 创建 2-19.jsp 页面，具体代码如下：

```
<%@ page contentType="text/html;charset=utf-8" %>
<html><body bgcolor=cyan>
<H3><% include file="Hello.txt" %>
</H3>
</body>
</html>
```