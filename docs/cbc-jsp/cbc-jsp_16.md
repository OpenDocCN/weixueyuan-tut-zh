# JSP 指令应用实例

## 实训内容和要求

编写三个 JSP 页面：first.jsp、second.jsp 和 third.jsp。另外，要求用“记事本”编写一个 hello.txt 文件。

hello.txt 中的每行都有若干个英文单词，这些单词之间用空格分隔，每行之间用 <br> 分隔，如下所示：

```
package apple void back public
<br>
private throw class hello welcome
```

## 实训步骤

(1) first.jsp 的具体要求。

first.jsp 使用 page 指令设置 contentType 属性的值为 text/plain，使用 include 指令静态插入 hello.txt 文件。first.jsp 的代码如下：

```
<%@ page contentType="text/plain" %>
<html>
<body>
<font Size=4 color=blue>
<%@ include file="hello.txt" %>
</font>
</body>
</html>
```

(2) second.jsp 的具体要求。

second.jsp 使用 page 指令设置 contentType 属性的值为 application/vn.ms-powerpoint，使用 include 指令静态插入 hello.txt 文件。second.jsp 的代码如下：

```
<%@ page contentType="application/vnd.ms-powerpoint" %>
<html>
<body>
<font Size=2 color=blue>
<%@ include file="hello.txt" %>
</font>
</body>
</html>
```

(3) third.jsp 的具体要求。

third.jsp 使用 page 指令设置 contentType 属性的值为 application/msword，使用 include 指令静态插入 hello.txt 文件。third.jsp 的代码如下：

```
<%@ page contentType="application/msword" %>
<html>
<body>
<font Size=4 color=blue>
<%@ include file="hello.txt" %>
</font>
</body>
</html>
```