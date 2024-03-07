# PHP 标记（4 种风格）

> 原文：[`c.biancheng.net/view/7256.html`](http://c.biancheng.net/view/7256.html)

所谓标记就是为了便于与其他内容区分而使用的一种特殊符号（例如 <?php、?>）。在 PHP 中，当解析一个 .php 格式的文件时，PHP 会寻找开始和结束标记，这两个标记之间的任何文本都会被解释成 PHP 代码。而在标记之外的任何文本会被认为是常规的 HTML，也就是说 PHP 标记可以起到隔离 PHP 代码和 HTML 的作用。

PHP 中一共有四种标记风格，下面就为大家一一介绍。

## 1\. XML 风格

```

<?php
    echo "C 语言中文网";
?>
```

这是本教程中使用最多的标记风格，同时它也是 PHP 推荐使用的标记风格。

这种风格是默认开启的而且不能禁用，因此可以保证在所有服务器上都可以使用这种风格的标记，特别是在编写一些用于不同服务器环境的应用程序时，这种标记风格尤为重要。

## 2\. 简短风格

```

<?
    echo "C 语言中文网";
?>
```

这种标记风格是最简单的，要使用这种标记风格，需要在配置文件 php.ini 中启用 short_open_tag 选项。不过在日常的开发中并不推荐使用这种风格的标记，因为这种标记在许多环境的默认设置中已经不支持了。

## 3\. ASP 风格（已移除）

```

<%
    echo "C 语言中文网";
%>
```

这种标记风格与 ASP 或 ASP.NET 的标记风格相同，默认情况下这种风格是禁用的。如果想要使用它需要在配置设定中启用了 asp_tags 选项。

不过该标记风格在 PHP7 中已经不再支持，大家了解即可。

## 4\. SCRIPT 风格（已移除）

```

<script language="php">
    echo 'C 语言中文网';
</script>
```

这种标记风格是最长的，如果读者使用过 JavaScript 或 VBScript，就会熟悉这种风格。

该标记风格在 PHP7 中已经不再支持，大家了解即可。

注意：如果文件内容是纯 PHP 代码，最好将文件末尾的 PHP 结束标记省略。这样可以避免在 PHP 结束标记之后，意外插入了空格或者换行符之类的误操作，而导致输出结果中意外出现空格和换行。