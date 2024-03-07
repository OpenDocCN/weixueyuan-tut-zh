# 正则表达式的处理选项

在 C#中，你可以使用[Regex(String, RegexOptions)构造函数](http://msdn2.microsoft.com/zh-cn/library/h5845fdz.aspx "MSDN 相关文档")来设置正则表达式的处理选项。如：Regex regex = new Regex("\ba\w{6}\b", RegexOptions.IgnoreCase);

上面介绍了几个选项如忽略大小写，处理多行等，这些选项能用来改变处理正则表达式的方式。下面是.Net 中常用的正则表达式选项：

表 6.常用的处理选项

| 名称 | 说明 |
| IgnoreCase(忽略大小写) | 匹配时不区分大小写。 |
| Multiline(多行模式) | 更改^和$的含义，使它们分别在任意一行的行首和行尾匹配，而不仅仅在整个字符串的开头和结尾匹配。(在此模式下,$的精确含意是:匹配\n 之前的位置以及字符串结束前的位置.) |
| Singleline(单行模式) | 更改.的含义，使它与每一个字符匹配（包括换行符\n）。 |
| IgnorePatternWhitespace(忽略空白) | 忽略表达式中的非转义空白并启用由#标记的注释。 |
| RightToLeft(从右向左查找) | 匹配从右向左而不是从左向右进行。 |
| ExplicitCapture(显式捕获) | 仅捕获已被显式命名的组。 |
| ECMAScript(JavaScript 兼容模式) | 使表达式的行为与它在 JavaScript 里的行为一致。 |

一个经常被问到的问题是：是不是只能同时使用多行模式和单行模式中的一种？答案是：不是。这两个选项之间没有任何关系，除了它们的名字比较相似（以至于让人感到疑惑）以外。