# Python __doc__ 属性：查看文档

前面介绍了使用 help() 函数来查看程序单元的帮助信息。比如导入 string 模块之后，即可使用 help() 函数来查看指定程序单元的帮助信息。

例如，在交互式解释器中输入如下命令来查看 string 模块下 capwords() 函数的作用：

```
>>> help(string.capwords)
Help on function capwords in module string:

capwords(s, sep=None)
    capwords(s [,sep]) -> string

    Split the argument into words using split, capitalize each
    word using capitalize, and join the capitalized words using
    join.  If the optional second argument sep is absent or None,
    runs of whitespace characters are replaced by a single space
    and leading and trailing whitespace are removed, otherwise
    sep is used to split and join the words.
```

通过上面描述可以看到，capwords() 函数的作用就是将给定的 s 字符串中每个单词的首字母变成大写的。该函数可通过 sep 参数指定分隔符：如果不指定 sep 参数，该字符串默认以空白作为分隔符。

在查看了帮助信息之后，接下来通过如下命令来测试 string.capwords() 函数的用法：

>>>string.capwords('abc xyz')
'Abc Xyz'
>>> string.capwords('abc;xyz',sep=';')
'Abc;Xyz'

上面代码在第一次使用 capwords() 函数时，没有指定 sep 参数，因此默认以空格为分隔符，这意味着程序将 `abc xyz` 分成 abc 和 xyz 两个单词，因此该函数将 a、x 两个字母变成大写的：在第二次使用 capwords() 函数时，指定 sep 参数为“;”，这意味着以“,”为分隔符将 `abc;xyz` 分成 abc 和 xyz 两个单词，因此程序将 a、x 两个字母变成大写的。

需要说明的是，使用 help() 函数之所以能查看到程序单元的帮助信息，其实完全是因为该程序单元本身有文档信息，也就是有 __doc__ 属性。换句话说，使用 help() 函数查看的其实就是程序单元的 __doc__ 属性值。

例如，使用 `print(string.capwords.__doc__)` 命令来查看 capwords() 的帮助信息，将会看到如下输出结果：

>>> print(string.capwords.__doc__)
capwords(s [,sep]) -> string

    Split the argument into words using split, capitalize each
    word using capitalize, and join the capitalized words using
    join.  If the optional second argument sep is absent or None,
    runs of whitespace characters are replaced by a single space
    and leading and trailing whitespace are removed, otherwise
    sep is used to split and join the words

对比 `help(string.capwords)` 和 `print(string.capwords.__doc__)` 两个命令的输出结果，不难看到它们的输出结果完全相同，这说明使用 help() 函数查看的就是程序单元的 __doc__ 属性值。

从理论上说，应该为每个程序单元都编写完备而详细的文档信息，这样开发者只要通过 help() 函数即可查看该程序单元的文档信息，完全不需要查看文档。但不得不说的是，有些程序单元的文档信息并不是很详细，此时可能需要借助于 Python 库的参考文档：[`docs.python.org/3/library/index.html`](https://docs.python.org/3/library/index.html).