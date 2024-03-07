# JSP 函数标签库

> 原文：[`c.biancheng.net/view/4493.html`](http://c.biancheng.net/view/4493.html)

在 JSP 页面中调用 JSTL 中的函数时需要使用 EL 表达式，调用语法格式如下：

${fn:函数名(参数 1,参数 2,...)}

下面介绍几个常用的函数。

#### 1）contains 函数

该函数的功能是判断一个字符串中是否包含指定的子字符串，如果包含，则返回 true，否则返回 false。其定义如下：

contains(string,substring)

该函数的调用示例代码如下：

${fn:contains("I am studying","am")}

上述 EL 表达式将返回 true。

#### 2）contain sIgnoreCase 函数

该函数与 contains 函数的功能相似，但判断是不区分大小写的。其定义如下：

containsIgnoreCase(string,substring)

该函数的调用示例代码如下：

${fn:containsIgnoreCase("I AM studying","am")}

上述 EL 表达式将返回 true。

#### 3）endsWith 函数

该函数的功能是判断一个字符串是否以指定的后缀结尾。其定义如下：

endsWith(string,suffix)

该函数的调用示例代码如下：

${fn:endsWith("I AM studying","am")}

上述 EL 表达式将返回 false。

#### 4）indexOf 函数

该函数的功能是返回指定子字符串在某个字符串中第一次出现时的索引，找不到时将返回 −1。其定义如下：

indexOf(string,substring)

该函数的调用示例代码如下：

${fn:indexOf("I am studying","am")}

上述 EL 表达式将返回 2。

#### 5）join 函数

该函数的功能是将一个 String 数组中的所有元素合并成一个字符串，并用指定的分隔符分开。其定义如下：

join(array,separator)

例如，假设一个 String 数组 my，它有 3 个元素，即“I”“am”和“studying”，那么下列 EL 表达式将返回“I, am, studying”。

${fn:join(my,",")}

#### 6）length 函数

该函数的功能是返回集合中元素的个数或者字符串中字符的个数。其定义如下：

length(input)

该函数的调用示例代码如下：

${fn:length("aaa")}

上述 EL 表达式将返回 3。

#### 7）replace 函数

该函数的功能是将字符串中出现的所有 beforestring 用 afterstring 替换，并返回替换后的结果。其定义如下：

replace(string,beforestring,afterstring)

该函数的调用示例代码如下：

${fn:replace("I am am studying","am","do")}

上述 EL 表达式将返回“I do do studying”。

#### 8）split 函数

该函数的功能是将一个字符串使用指定的分隔符 separator 分离成一个子字符串数组。其定义如下：

该函数的调用示例代码如下：

<c:set var="my" value="${fn:split('I am studying','')}"/>
<c:forEach var="myArrayElement" items="${my}">
    ${myArrayElement}</br>
</c:forEach>

上述示例代码的显示结果如图 1 所示。

#### 9）startsWith 函数

该函数的功能是判断一个字符串是否以指定的前缀开头。其定义如下：

startsWith(string,prefix)

该函数的调用示例代码如下：

${fn:startsWith("I AM studying","am")}

上述 EL 表达式将返回 false。

#### 10）substring 函数

该函数的功能是返回一个字符串的子字符串。其定义如下：

substring(string,begin,end)

该函数的调用示例代码如下：

${fn:substring("abcdef",1,3)}

上述 EL 表达式将返回“bc”。

#### 11）toLowerCase 函数

该函数的功能是将一个字符串转换成它的小写版本。其定义如下：

toLowerCase(string)

该函数的调用示例代码如下：

${fn:toLowerCase("I AM studying")}

上述 EL 表达式将返回"i am studying"。

#### 12）toUpperCase 函数

该函数的功能与 toLowerCase 函数的功能相反，这里不再赘述。

#### 13）trim 函数

该函数的功能是将一个字符串开头和结尾的空白去掉。其定义如下：

trim(string)

该函数的调用示例代码如下：

${fn:trim("I AM studying")}

上述 EL 表达式将返回“I AM studying”。