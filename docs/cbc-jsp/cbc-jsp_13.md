# JSP 指令详解（三大指令）

JSP 指令是为 JSP 引擎（比如 Tomcat）而设计的，它们并不直接产生任何可见输出，而只是告诉引擎如何处理 JSP 页面中的其余部分。

JSP 引擎会根据 JSP 的指令信息来编译 JSP，生成 Java 文件。在生成的 Java 文件中，指令就不存在了。

一般都会把 JSP 指令放到 JSP 文件的最上方，但这不是必须的。

指令通常以`<%@`标记开始，以`%>`标记结束，它的具体语法如下：

<%@  指令名称  属性 1="属性值 1"  属性 2="属性值 2" ... 属性 n="属性值 n" %>

JSP 中三大指令，它们分别是 page、include 和 taglib，下表是对它们的简要说明。

JSP 三大指令

| 指 明 | 说 明 |
| page | page 指令用于定义 JSP 页面的各种属性。page 是最复杂的一个指令，它的属性很多，常用的属性有 import、language、pageEncoding 等。 |
| include | 静态包含指令，用于引入其它 JSP 文件。使用 include 指令引入的其它 JSP 文件，JSP 引擎将把这两个 JSP 文件翻译成一个 servlet，所以将 include 称为静态包含。 |
| taglib | 用于在 JSP 页面中导入标签库（JSP 标准标签库、第三方标签库、自定义标签库）。 |

本教程重点讲解了 page 指令和 include 指令。