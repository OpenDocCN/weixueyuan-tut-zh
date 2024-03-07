# XML 的基本语法

开发 JSP 的编程技术人员必须掌握 XML 的基本语法以及规范，才能正确使用 XML。

## XML 文档的基本结构

XML 文档的基本结构如下：

```
<?xml version="1.0" encoding="UTF-8"?>    //文档声明
<users>
<user>
<name>白素素</name>
<english-name>baisusu</english-name>
<age>22</age>
<sex>女</sex>
<address>北京市</address>
<description>她是一个作家</description>
</user>
</users>
```

建立 XML 文档必须要有 XML 文档的声明，如第 1 行的声明：

```
<?xml version="1.0" encoding="UTF-8"?>
```

其中，version 是指该文档遵循的 XML 的标准版本，encoding 指声明文档使用的字符编码格式。

## 标记必须闭合

在 XML 文档中，除 XML 声明外，所有元素都必须有其结束标识，如果 XML 元素没有文本节点，则釆用自闭合的方式关闭节点。例如，以 <note/>这样的形式进行自闭合。

正常的标记闭合形式如下：

```
<age>22</age>
<sex>女</sex>
<address>北京市</address>
```

age、sex、address 都有相应的结束标记。

## 合理地嵌套

在 XML 文档中，合理的元素嵌套是必须的，如下嵌套是错误的，会导致 XML 错误，且描述不清。

```
<age>22<sex>
</age>女</sex>
```

正确的描述如下：

```
<age>22</age>
<sex>女</sex>
```

## XML 元素

XML 元素是指成对出现的标记，且每个元素之间有层级关系。例如，<sex> 元素指的是 <sex>女</sex>。

<user> 元素指的是：

```
<user>
<name>白素素</name>
<english-name>baisusu</english-name>
<age>22</age>
<sex>女</sex>
<address>北京市</address>
<description>她是一个作家</description>
</user>
```

其中，<age> 元素是 <user> 元素的子元素，<user> 元素是 <age> 元素的父元素；两个 <user> 元素是并列关系。

元素的命名规则如下：

*   可以包含字母、数字、其他字符。
*   不能以 xml 开头，包括其大小写，如 XML、xMl 等。
*   不能以数字或者标点符号开头，不能包含空格。
*   XML 文档除了 XML 以外，没有其他的保留字，任何名字都可以使用，但是应该尽量使元素名字具有可读性。
*   尽量避免使用和，以免引起混乱，可以使用下划线。
*   XML 元素命名中不要使用，因为 XML 命名空间需要用到这个特殊字符。例如，下面的命名是正确的：

<title2>
<title_name>

下面这些命名是错误的：

<2title>
<xmlTtle>
<titel name>
<.age>

注意，所有的 XML 文档有且只有一个根元素来定义整个文档。例如，在 web.xml 代码中，可以看到 <web-app> 就是它的根元素。例如，下面的 web.xml 定义是错误的：

```
<web-app version="java">
xmln="http://java.sun.com/xml/ns/javaee"
xmln:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:chemaLocation="http://java.sun.com/xml/ns/javaee
http://java.sun.com/xml/ns/javaee/web-app_2.xsd">
...
</web-app>
<web-app>
...
</web-app>
```

## XML 属性

XML 元素可以在开始标记中包含属性，类似 HTML，属性（Attribute）提供关于元素的额外（附加）信息，它被定义在 XML 元素的标记中，且自身有对应的值。例如，<user> 元素的属性名和属性值如下：

```
<user language="java">
<name>白素素</name>
<english-name>baisusu</english-name>
<age>22</age>
<sex>女</sex>
<address>北京市</address >
<description>她是一个作家</description>
</user>
```

属性的命名规则跟元素的命名规则一样。

提示：属性值必须带有单引号或双引号，如果属性值本身包含双引号，那么有必要使用单引号包围它可以使用实体（&quot）引用。

## 语法编写要点

#### 1\. 大小写敏感

XML 文档要注意字母的大小写，标记名称、属性名和属性值都是大小写敏感的。例如，<age> 和 <Age> 是不同的。

一般初学者刚写 XML 文档时常犯的错误就是，开始标记与结束标记的大小写不一致而导致出现错误。例如：

```
<name>Chen Lin</Name>
```

<name> 与 </Name> 不能相互匹配，而导致 <name> 没有正确地结束标记而被关闭。

正确的写法如下；

```
<name>Chen Lin</name>
```

#### 2\. 注释的写法

XML 注释的形式如下：

<!--注释单行-->
<!--
    注释多行
-->

#### 3\. 空白被保留

空白被保留是指在 XML 文档中，空白部分并不会被解析器删除，而是被当作数据一样完整地保留。例如：

```
<description>入夜渐微凉 繁花落地成霜</description>
```

“入夜渐微凉 繁花落地成霜”中的空白会被当作数据保留。

#### 4\. 转义字符的使用

XML 中有些特殊字符需要转义，比如 >、<、&、单引号、双引号等，其转义字符和 HTML 中的转义字符是一样的。

#### 5\. CDATA 的使用

CDATA 用于需要原文保留的内容，尤其是在解析 XML 过程中产生歧义的部分，当某个节点的数据有大量需转义的字符时，CDATA 就可以发挥作用了，其用法如下：

<! [CDATA [
    内容
] ] >

举例说明：

```
<! [CDATA [
if(m>n)
{
    alert(m 大于 n);
}
else if(m﹤n&&m!=0)
{
    alert(m 小于 n);
}
] ] >
```

提示：CDATA 不能嵌套使用。