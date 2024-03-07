# Go 语言的关键字与标识符

> 原文：[`www.weixueyuan.net/a/422.html`](http://www.weixueyuan.net/a/422.html)

标识符就是标识某个实体的一个符号，也就是给实体起的名字，从而将其与其他实体区分开。例如，在日常生活中有许多交通工具，发明者给它们起了名字，如下图所示。

![生活中的标识符](img/52114f792e492be926b9eec97ff84b35.png)
图：生活中的标识符
当然，在不同的环境下，标识符含义也不相同。比如嬴政，在现实生活中是历史人物秦始皇，而在游戏《王者荣耀》中就是一个法师英雄，如下图所示。

![嬴政](img/271c00f739503ab86573b1e6604ce49c.png)
图：嬴政

## 1、预定义标识符

预定义标识符是 Go 语言系统预先定义的标识符，具有见名知义的特点，例如函数“输出”（printf）、“新建”（new）、“复制”（copy）等。预定义标识符可以作为用户标识符使用，只是这样会失去系统规定的原意，使用不当还会使程序出错。下面列举了 36 个预定义标识符，如下表所示。

表：Go 语言预定义标识符

| append | bool | byte | сар | close | complex | complex64 | complex128 | uint16  |
| copy | false | float32 | float64 | imag | int | int8 | int16 | uint32 |
| int32 | int64 | iota | len | make | new | nil | panic | uint16 |
| print | println | real | recover | string | true | uint  | uint8 | uintptr |

## 2、关键字

Go 语言的关键字是系统自带的，是具有特殊含义的标识符。Go 语言中内置了 25 个关键字用于开发。下面列举了 Go 语言代码中会使用到的 25 个关键字或保留字，如下表所示。

表：Go 语言关键字或保留字

| break | default | func | interface | select |
| case | defer | go | map | struct |
| chan | else | goto | package | switch |
| const | fallthrough | if | range | type |
| for | import  | return | var | continue |

## 3、自定义标识符

用户根据需要自定义的标识符，一般用来给变量、类型、函数等程序实体起名字。

自定义标识符实际上是一个或多个字母`A~Z 和 a~z`、数字`0~9`、下画线`_`等组成的序列，但是第一个字符必须是字母或下画线，而不能是数字。

Go 语言不允许在自定义标识符中使用“@”、“$”或“%”等符号，也不允许将关键字用作自定义标识符。如果将预定义标识符用作自定义标识符，那么自定义标识符的含义会覆盖预定义标识符，容易造成程序混乱。另外，Go 语言是一种区分大小写的编程语言。因此，Manpower 和 manpower 是两个不同的标识符。

错误的自定义标识符示例如下表所示：

| 错误的自定义标识符 | 错误原因 |
| 1xy | 以数字开头 |
| case | Go 语言的关键字 |
| chan | Go 语言的关键字 |
| nil | 预定义标识符 |

需要注意的是，在现实生活中，名字可以重复，比如可能有很多人叫作张三，但是在 Go 语言中，标识符绝对不能重复，要确保每个标识符都代表一个独立的个体。