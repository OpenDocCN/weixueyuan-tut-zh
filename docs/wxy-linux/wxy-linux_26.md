# Linux 帮助命令

> 原文：[`www.weixueyuan.net/a/153.html`](http://www.weixueyuan.net/a/153.html)

Linux 下常用的帮助命令有 man 和 help。

## man 命令

对于绝大部分 Linux 终端用户和 C 语言程序员而言，经常需要查询一些命令或函数的具体使用方法，此时可以使用 Linux 自带的 man 帮助文件命令。

只要在命令 man 后输入想要获取的命令的名称（如 ls），man 就会列出一份完整的说明，其内容包括命令语法、各选项的意义及相关命令等。该命令使用方式如下：

man [选项] 命令名称

man 命令选项说明如表 1 所示。

表 1：man 命令选项说明

| 选项 | 说明 |
| --- | --- |
| -f | 只显示出命令的功能而不显示其中详细的说明文件 |
| -w | 不显示手册页，只显示将被格式化和显示的文件所在位置。 |
| -a | 显示所有的手册页，而不是只显示第一个。 |
| -E | 在每行的末尾显示 $ 符号 |

## help 命令

help 命令用于查看所有 shell 命令。用户可以通过该命令寻求 shell 命令的用法，只需在所查找的命令后输入 help 命令，就可以看到所查命令的内容了。例如，输入`cd-help`便可查看 cd 命令的使用方法。

info 命令用来获取相关命令的详细使用方法，例如，`info ls`可以获取使用 ls 的详细信息。