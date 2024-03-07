# Python 查看模块（变量、函数、类）方法

在导入模块之后，开发者往往需要了解模块包含哪些功能，比如包含哪些变量、哪些函数、哪些类等，还希望能查看模块中各成员的帮助信息，掌握这些信息才能正常地使用该模块。

为了查看模块包含什么，可以通过如下两种方式：

1.  使用 dir() 函数。
2.  使用模块本身提供的 __all__ 变量。

前面章节已经介绍过 dir() 函数的基本用法，该函数可用于返回模块或类所包含的全部程序单元（包括变量、函数、类和方法等），但直接使用 dir() 函数默认会列出模块内所有的程序单元，包括以下画线开头的程序单元，而这些以下画线开头的程序单元其实并不希望被外界使用。

比如在 Python 的交互式解释器中执行如下命令来导入 string 模块（Python 内置的用于丰富字符串功能的模块）：

>>> import string

然后通过 dir() 函数来查看该模块的内容，将可以看到如下输出结果：

>>> dir(string)
['Formatter', 'Template', '_ChainMap', '_TemplateMetaclass', '__all__', '__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', '_re', '_string', 'ascii_letters', 'ascii_lowercase', 'ascii_uppercase', 'capwords', 'digits', 'hexdigits', 'octdigits', 'printable', 'punctuation', 'whitespace']

很明显，该模块内有大量以下画线开头的程序单元，其实这些程序单元并不希望被其他程序使用，因此列出这些程序单元意义不大。

为了过滤这些以下画线开头的程序单元，我们可以使用如下列表推导式来列出模块中的程序单元：

>>> [e for e in dir(string) if not e.startswith('_')]
['Formatter', 'Template', 'ascii_letters', 'ascii_lowercase', 'ascii_uppercase', 'capwords', 'digits', 'hexdigits', 'octdigits', 'printable', 'punctuation', 'whitespace']

此命令使用 for 循环的列表推导式列出了 dir(string) 返回的所有不以下画线开头的程序单元，它们才是该模块希望被其他程序使用的程序单元。

此外，本章前面还介绍过模块中的 __all__ 变量，该变量相当于该模块开放的功能接口，因此也可通过该模块的 __all__ 变量来查看模块内的程序单元。例如，在交互式解释器中输入如下命令：

>>> string.__all__
['ascii_letters', 'ascii_lowercase', 'ascii_uppercase', 'capwords', 'digits', 'hexdigits', 'octdigits', 'printable', 'punctuation', 'whitespace', 'Formatter', 'Template']

对比前面列表推导式列出的结果和此处 __all__ 变量列出的结果，不难发现二者的输出结果大致相同，这说明使用这两种方式都可以查看到模块所包含的程序单元。

注意，并不是所有模块都会提供 __all__ 变量的，有些模块并不提供 __all__ 变量，在这种情况下，只能使用列表推导式来查看模块中的程序单元。