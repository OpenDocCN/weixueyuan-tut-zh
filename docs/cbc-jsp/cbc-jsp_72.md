# XML 是什么？

万维网协会推出的一套数据交换标准——XML，是一种可扩展的标记语言，被设计用来传输和存储数据。XML 可用于定义 Web 网页上的文档元素以及复杂数据的表述和传输。

Extensible Markup Language，简称 XML，中文含义是可扩展标记语言，该语言与 HTML 类似，主要功能是传输数据、储存数据和共享数据。XML 语言没有规定的标签体，需要自定义标签，是一种自我描述的语言。XML 与 HTML 有着本质的区别，XML 传输和存储数据，而 HTML 用来显示数据。

XML 的最大特点是自我描述和任意扩展，当用其描述数据时，用户可以根据需要，组织符合 XML 规范形式的任意内容，并且标签的名称也可以由用户指定。XML 的定义格式如下：

```
<?xml version="1.0" encoding="UTF-8"?>
<user>
<name>白素素</name>
<english-name>baisusu</english-name>
<age>22</age>
<sex>女</sex>
<address>北京市</address>
<description>她是一个作家</description>
</user>
```

上述代码定义的是白素素的基本信息，包括姓名、英文名称、性别、年龄、住址、职业等信息。上述的内容，同样可以用下面的自定义形式进行描述：

```
<?xml version="1.0" encoding="UTF-8"?>
<user>
<property name="name" value="白素素"/>
<property name="english_name" value="baisusu"/>
<property name="age" value="22"/>
<property name="sex" value="女"/>
<property name="address" value="北京市"/>
<property name="description" value="她是一个作家"/>
</user>
```

无论用哪种结构格式，它都能清楚地描述用户的基本信息，这就体现了 XML 的可扩展和自定义标签的特点。

提示：XML 其实是一个文本文件，开发工具有 Editplus、UEStudio、MyEcHpse 的 XML 编辑器、XMLSpy 等。