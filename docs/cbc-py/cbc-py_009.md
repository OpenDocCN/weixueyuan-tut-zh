# Python 变量命名规则（超级详细）

Python 需要使用标识符给变量命名，其实标识符就是用于给程序中变量、类、方法命名的符号（简单来说，标识符就是合法的名字）。

Python 语言的标识符必须以字母、下画线（_）开头，后面可以跟任意数目的字母、数字和下画线（_）。此处的字母并不局限于 26 个英文字母，可以包含中文字符、日文字符等。

由于 Python 3 支持 UTF-8 字符集，因此 Python 3 的标识符可以使用 UTF-8 所能表示的多种语言的字符。Python 语言是区分大小写的，因此 abc 和 Abc 是两个不同的标识符。

Python 2.x 对中文支持较差，如果要在 Python 2.x 程序中使用中文字符或中文变量，则需要在 Python 源程序的第一行增加“#coding:utf-8”，当然别忘了将源文件保存为 UTF-8 字符集。

在使用标识符时，需要注意如下规则：

1.  标识符可以由字母、数字、下画线（_）组成，其中数字不能打头。
2.  标识符不能是 Python 关键字，但可以包含关键字。
3.  标识符不能包含空格。

例如下面变量，有些是合法的，有些是不合法的：

*   abc_xyz：合法。
*   HelloWorld：合法。
*   abc：合法。
*   xyz#abc：不合法，标识符中不允许出现“#”号。
*   abc1：合法。
*   1abc：不合法，标识符不允许数字开头。

## Python 的关键字和内置函数

Python 还包含一系列关键字和内置函数，一般也不建议使用它们作为变量名：

*   如果开发者尝试使用关键字作为变量名，Python 解释器会报错。
*   如果开发者使用内置函数的名字作为变量名，Python 解释器倒不会报错，只是该内置函数就被这个变量覆盖了，该内置函数就不能使用了。

Python 包含了如表 1 所示的关键字：

表 1 Python 关键字

| False | None | True | and | as |
| assert  | break | class | continue | def |
| del | elif | else  | except | finally |
| for | from | global | if | import |
| in | is | lambda | nonlocal | not |
| or | pass | raise | return | try |
| while | with | yield |   |   |

实际上 Python 非常方便，开发者可以通过 Python 程序来查看它所包含的关键字。例如，对于如下程序：

```
#导入 keyword 模块
import keyword
#显示所有关键字
keyword.kwlist
```

从上面代码可以看出，程序只要先导入 keyword 模块，然后调用 keyword.kwlist 即可查看 Python 包含的所有关键字。运行上面程序，可以看到如下输出结果：

['False','None','True','and','as','assert','break','class','continue','def','del','elif','else','except','finally','for','from','global','if','import','in','is','lambda','nonlocal','not','or','pass','raise','return','try','while','With','yield']

上面这些关键字都不能作为变量名。

此外，Python 3 还提供了如表 2 所示的内置函数。

表 2 Python 内置函数

| abs() | all() | any() | basestring() | bin() |
| bool() | bytearray() | callable() | chr() | classmethod() |
| cmp() | compile()  | complex() | delattr() | dict() |
| dir() | divmod() | enumerate()  | eval() | execfile() |
| file() | filter() | float()  | format() | frozenset() |
| getattr() | globals() | hasattr() | hash() | help() |
| hex() | id() | input() | int()  | isinstance() |
| issubclass() | iter() | len() | list() | locals() |
| long() | map()  | max() | memoryview() | min() |
| next()  | object() | oct()  | open()  | ord() |
| pow() | print() | property() | range() | raw_input() |
| reduce() | reload() | repr() | reversed() | zip() |
| round()  | set() | setattr() | slice() | sorted() |
| staticmethod() | str() | sum() | super() | tuple() |
| type() | unichr() | unicode()  | vars() | xrange() |
| Zip() | __import__() | apply() | buffer() | coerce() |
| intern |   |   |   |   |

上面这些内置函数的名字也不应该作为标识符，否则 Python 的内置函数会被覆盖。

注意：在 Python 2.x 中，print 是关键字而不是函数。上面这些内置函数（如 unicode()）只是 Python 2.x 的内置函数，为了保证 Python 程序具有更好的兼容性，程序也不应该使用这些内置函数的名字作为标识符。