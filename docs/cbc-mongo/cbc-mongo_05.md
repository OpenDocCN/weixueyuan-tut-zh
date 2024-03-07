# BSON 对 JSON 做了哪些改进？

> 原文：[`c.biancheng.net/view/6547.html`](http://c.biancheng.net/view/6547.html)

在《MongoDB 的文档数据模型》一节中介绍过，MongoDB 存储的数据格式与 JSON 十分类似，MongoDB 所采用的数据格式被称为 BSON，是一种基于 JSON 的二进制序列化格式，用于 MongoDB 存储文档并进行远程过程调用。

JSON 是一种网络常用的数据格式，具有自描述性。JSON 的数据表示方式易于解析，但支持的数据类型有限。BSON 目前主要用于 MongoDB 中，选择 JSON 进行改造的原因主要是 JSON 的通用性及 JSON 的 schemaless 的特性。

BSON 改进的主要特性有下面三点。

## 更快的遍历速度

BSON 对 JSON 的一个主要的改进是，在 BSON 元素的头部有一个区域用来存储元素的长度， 当遍历时，如果想跳过某个文档进行读取，就可以先读取存储在 BSON 元素头部的元素的长度， 直接 seek 到指定的点上就完成了文档的跳过。

在 JSON 中，要跳过一个文档进行数据读取，需要在对此文档进行扫描的同时匹配数据结构才可以完成跳过操作。

## 操作更简易

如果要修改 JSON 中的一个值，如将 9 修改为 10，这实际是将一个字符变成了两个，会导致其后面的所有内容都向后移一位。

在 BSON 中，可以指定这个列为整型，那么，当将 9 修正为 10 时，只是在整型范围内将数字进行修改，数据总长不会变化。

需要注意的是：如果数字从整型增大到长整型，还是会导致数据总长增加。

## 支持更多的数据类型

BSON 在 JSON 的基础上增加了很多额外的类型，BSON 增加了“byte array”数据类型。这使得二进制的存储不再需要先进行 base64 转换再存为 JSON，减少了计算开销。

BSON 支持的数据类型如表所示。

BSON 支持的数据类型

| 类型 | 描述示例 |
| NULL | 表示空值或者不存在的字段，{"x" : null} |
| Boolean | 布尔型有 true 和 false，{"x" : true} |
| Number | 数值：客户端默认使用 64 位浮点型数值。{"x" : 3.14} 或 {"x" : 3}。对于整型值，包括 NumberInt（4 字节符号整数）或 NumberLong（8 字节符号整数），用户可以指定数值类型，{"x" : NumberInt("3")} |
| String | 字符串：BSON 字符串是 UTF-8，{"x" : "中文"} |
| Regular Expression | 正则表达式：语法与 JavaScript 的正则表达式相同，{"x" : /[cba]/} |
| Array | 数组：使用“[]”表示，{"x" : ["a", "b", "c"]} |
| Object | 内嵌文档：文档的值是嵌套文档，{"a" : {"b" : 3}} |
| ObjectId | 对象 id：对象 id 是一个 12 字节的字符串，是文档的唯一标识，{"x" : objectId()} |
| BinaryData | 二进制数据：二进制数据是一个任意字节的字符串。它不能直接在 Shell 中使用。如果要将非 UTF-8 字符保存到数据库中，二进制数据是唯一的方式 |
| JavaScript | 代码：查询和文档中可以包括任何 JavaScript 代码，{"x" : function(){/*...*/}} |
| Data | 日期：{"x" : new Date()} |
| Timestamp | 时间戳：var a = new Timestamp() |