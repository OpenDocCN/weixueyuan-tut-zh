# Spring MVC 的表单标签库详解

> 原文：[`c.biancheng.net/view/4420.html`](http://c.biancheng.net/view/4420.html)

表单标签库中包含了可以用在 JSP 页面中渲染 HTML 元素的标签。在 JSP 页面使用 Spring 表单标签库时，必须在 JSP 页面开头处声明 taglib 指令，指令代码如下：

<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>

在表单标签库中有 form、input、password、hidden、textarea、checkbox、checkboxes、radiobuttton、radiobuttons、select、option、options、errors 等标签。

| 名称 | 作用 |
| form | 渲染表单元素 |
| input | 渲染 <input type="text"/> 元素 |
| password | 渲染 <input type="password"/> 元素 |
| hidden | 渲染 <input type="hidden"/> 元素 |
| textarea | 渲染 textarea 元素 |
| checkbox | 渲染一个 <input type="checkbox"/> 元素 |
| checkboxes | 渲染多个 <input type="checkbox"/> 元素 |
| radiobutton | 渲染一个 <input type="radio"/> 元素 |
| radiobuttons | 渲染多个 <input type="radio"/> 元素 |
| select | 渲染一个选择元素 |
| option | 渲染一个选项元素 |
| options | 渲染多个选项元素 |
| errors | 在 span 元素中渲染字段错误 |

## 表单标签

表单标签的语法格式如下：

<form:form modelAttribute="xxx" method="post" action="xxx">
    ...
</form:form>

表单标签除了具有 HTML 表单元素属性以外，还具有 acceptCharset、commandName、cssClass、cssStyle、htmlEscape 和 modelAttribute 等属性。

*   acceptCharset：定义服务器接受的字符编码列表。
*   commandName：暴露表单对象的模型属性名称，默认为 command。
*   cssClass：定义应用到 form 元素的 CSS 类。
*   cssStyle：定义应用到 form 元素的 CSS 样式。
*   htmlEscape：true 或 false，表示是否进行 HTML 转义。
*   modelAttribute：暴露 form backing object 的模型属性名称，默认为 command。

其中，commandName 和 modelAttribute 属性的功能基本一致，属性值绑定一个 JavaBean 对象。假设控制器类 UserController 的方法 inputUser 是返回 userAdd.jsp 的请求处理方法，inputUser 方法的代码如下：

```

@RequestMapping(value="/input")
public String inputUser(Model model) {
    ...
    model.addAttribute("user", new User());
    return "userAdd";
}
```

userAdd.jsp 的表单标签代码如下：

<form:form modelAttribute="user" method="post" action="user/save">
    ...
</form:form>

注意：在 inputUser 方法中，如果没有 Model 属性 user，userAdd.jsp 页面就会抛出异常，因为表单标签无法找到在其 modelAttribute 属性中指定的 form backing object。

## input 标签

input 标签的语法格式如下：

<form:input path="xxx"/>

该标签除了有 cssClass、cssStyle、htmlEscape 属性以外，还有一个最重要的属性——path。path 属性将文本框输入值绑定到 form backing object 的一个属性。示例代码如下：

<form:form modelAttribute="user" method="post" action="user/save">
    <form:input path="userName"/>
</form:form>

上述代码将输入值绑定到 user 对象的 userName 属性。

## password 标签

password 标签的语法格式如下：

<form:password path="xxx"/>

该标签与 input 标签的用法完全一致，这里不再赘述。

## hidden 标签

hidden 标签的语法格式如下：

<form:hidden path="xxx"/>

该标签与 input 标签的用法基本一致，只不过它不可显示，不支持 cssClass 和 cssStyle 属性。

## textarea 标签

textarea 基本上就是一个支持多行输入的 input 元素，语法格式如下：

<form:textarea path="xxx"/>

该标签与 input 标签的用法完全一致，这里不再赘述。

## checkbox 标签

checkbox 标签的语法格式如下：

<form:checkbox path="xxx" value="xxx"/>

多个 path 相同的 checkbox 标签，它们是一个选项组，允许多选，选项值绑定到一个数组属性。示例代码如下：

<form:checkbox path="friends" value="张三"/>张三
<form:checkbox path="friends" value="李四"/>李四
<form:checkbox path="friends" value="王五"/>王五
<form:checkbox path="friends" value="赵六"/>赵六

上述示例代码中复选框的值绑定到一个字符串数组属性 friends（String[] friends）。该标签的其他用法与 input 标签基本一致，这里不再赘述。

## checkboxes 标签

checkboxes 标签渲染多个复选框，是一个选项组，等价于多个 path 相同的 checkbox 标签。它有 3 个非常重要的属性，即 items、itemLabel 和 itemValue。

*   items：用于生成 input 元素的 Collection、Map 或 Array。
*   itemLabel：items 属性中指定的集合对象的属性，为每个 input 元素提供 label。
*   itemValue：items 属性中指定的集合对象的属性，为每个 input 元素提供 value。

checkboxes 标签的语法格式如下：

<form:checkboxes items="xxx" path="xxx"/>

示例代码如下：

<form:checkboxes items="${hobbys}" path="hobby"/>

上述示例代码是将 model 属性 hobbys 的内容（集合元素）渲染为复选框。在 itemLabel 和 itemValue 省略的情况下，如果集合是数组，复选框的 label 和 value 相同；如果是 Map 集合，复选框的 label 是 Map 的值（value），复选框的 value 是 Map 的关键字（key）。

## radiobutton 标签

radiobutton 标签的语法格式如下：

<form:radiobutton path="xxx" value="xxx"/>

多个 path 相同的 radiobutton 标签，它们是一个选项组，只允许单选。

## radiobuttons 标签

radiobuttons 标签渲染多个 radio，是一个选项组，等价于多个 path 相同的 radiobutton 标签。radiobuttons 标签的语法格式如一下：

<form:radiobuttons items="xxx" path="xxx"/>

该标签的 itemLabel 和 itemValue 属性与 checkboxes 标签的 itemLabel 和 itemValue 属性完全一样，但只允许单选。

## select 标签

select 标签的选项可能来自其属性 items 指定的集合，或者来自一个嵌套的 option 标签或 options 标签。其语法格式如下：

<form:select path="xxx" items="xxx"/>

或

<form:select path="xxx" items="xxx">
    <option value="xxx">xxx</option>
</form:select>

或

<form:select path="xxx">
    <form:options items="xxx"/>
</form:select>

该标签的 itemLabel 和 itemValue 属性与 checkboxes 标签的 itemLabel 和 itemValue 属性完全一样。

## options 标签

options 标签生成一个 select 标签的选项列表，因此需要和 select 标签一同使用，具体用法参见“select 标签”。

## errors 标签

errors 标签渲染一个或者多个 span 元素，每个 span 元素包含一个错误消息。它可以用于显示一个特定的错误消息，也可以显示所有错误消息。其语法格式如下：

<form:errors path="*"/>

或

<form:errors path="xxx"/>

其中，“*”表示显示所有错误消息；“xxx”表示显示由“xxx”指定的特定错误消息。