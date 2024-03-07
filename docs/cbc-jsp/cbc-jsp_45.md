# EL 是什么？（详解）

表达式是 JSP 必备的语法元素，在接下来的教程中我们将介绍 EL、运算符、变量、常量、保留字的定义以及应用技巧。本文我们首先介绍

EL 表达式语言来自于标准化脚本语言 ECMAScript 和 XPath，EL 表达式简化了 JSP 语言的写法。在 EL 表达式出现之前，开发 Java Web 应用时经常需要将大量的 Java 代码嵌入 JSP 页面中，使页面的可读性变得很差，使用 EL 可以使页面变得很好。例如，对应于以下 Java 代码片段：

```
<%
if(session.getAttribute(nunamen)!==null)
{
    Out.println(session.getAtribute("uname").toString());
}
%>
```

如果使用 EL 表达式，则只需要下面一行代码：

```
$ {uname}
```

在 Web 开发中常用的表达式是 EL，它除了具有语法简单和使用方便的性质外，还具有以下几方面的特点。

1.  可以与 JSTL 以及 JavaScript 结合使用。
2.  可自动执行数值转换。例如，如果想输出两个字符串数值型 number1 和 number2 的和，可以通过“+”连接，即 ${numberl+number2}。
3.  可以访问 JavaBean 中的属性、嵌套属性和集合属性。
4.  可实现算术、逻辑、关系、条件等多种运算。
5.  可以获得命名和空间（pageContext 对象是页面中所有其他内置对象的最大范围的继承对象，通过它可以访问内置对象）。
6.  执行除法时如果除数是 0，则返回无穷大（Infinity），不返回错误。
7.  可访问 4 种 JSP 的作用域（request、session、application、page）。
8.  扩展函数可以与 Java 类的静态方法执行映射。

如果 Web 容器不支持 EL，可以禁用 EL，方法有三种。

第一种：使用斜杠符号“\”，该方法只须在 EL 表达式前加“\”。例如：

```
\${uname}
```

第二种：使用 page 指令，该方法将 page 指令中的 isELIgnored 设置为 true。例如：

```
<%@ page isEIiIgnored="true" %>
```

第三种：在 web.xml 文件中配置 <el-ignored> 元素。例如，下面的配置禁止 Web 中的所有 JSP 页面使用 EL：

```
<jsp-property-group>
    <url-pattern>*jsp</url-pattern>
    <el-ignored>false</el-ignored>
</jsp-propery-group>
```