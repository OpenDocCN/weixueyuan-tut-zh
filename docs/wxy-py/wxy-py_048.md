# Python 模块及导入方法

> 原文：[`www.weixueyuan.net/a/617.html`](http://www.weixueyuan.net/a/617.html)

模块就是一个 Python 文件。一个空的 Python 文件也可以称为模块。多数情况下，一个 Python 文件包含变量、函数和其他的内容，这些内容可以被其他代码使用。

使用模块的最简单方法就是“import 模块名”。下面在当前目录下创建一个文件 child.py，内容如下：

```

""" this is the child module      # 文件 child.py 的内容
"""    
name = "child"               # 定义一个变量
def hello():                 # 定义一个函数
    print("hello from child")
```

下面来操作这个模块：

```

>>> import child                  # 引入该模块
>>> child                         # child 现在就是一个模块
<module 'child' from '/moduleDemo1/child.py'>
>>> child.__doc__       # 查看该模块的 __doc__ 属性，即模块最开始的字符串
' this is the child module\n'
>>> child.__file__                # 模块的文件信息
'/moduleDemo1/child.py'
>>> child.__name__                # 模块名
'child'
>>> child.name                    # 访问该模块中的 name 变量
'child'
>>> child.hello                   # 查看 hello，其是一个函数
<function hello at 0x108fa3e18>
>>> child.hello()                 # 调用 hello()函数
hello from child
```

可以发现，基本用法就是 import 的某个模块，然后即可使用该文件中定义的变量、函数等资源。使用时带上模块的名，如 child.name 表示属于模块 child 的变量 name。

如果不希望将某个文件中所有的内容都引入进来，如只希望引入 hello() 函数，则可以使用下面的语句：

```

from child import hello
```

这样 child 模块下的 hello() 函数就被引入到当前空间，这时不再需要使用 child.hello，而应该使用 hello。

```

>>> from child import hello
>>> hello()
hello from child
```

如果不希望每次访问某个模块的资源都带上模块名，可以使用下面的方法：

```

from 模块名 import *
```

这样指定模块的内容都被加载到了当前空间，使用时便不需要再带上模块名。

```

>>> from child import *
>>> hello
<function hello at 0x10eef8c80>
>>> hello()
hello from child
>>> name
'child'
```

如果同时使用这两种方式，是否会发生冲突呢？答案是不会的，我们会发现两份资源共存，而且相互不影响。

```

>>> import child              # 同时使用两种方式
>>> from child import *
>>> id(child.name)            # 它们的 id 是不同的
4524178480
>>> id(name)
>>> id(name)
4530112248
>>> child.name
'child'
>>> name
'child'
>>> child.name = "new_name"   # 修改其中一个并不会影响另一个
>>> name
'child'
```

前面提到了在当前目录下创建一个文件，为何要在当前目录下呢？其他的目录可不可以呢？答案是不可以，因为只有某些特定目录下的文件才会被引入进来。这就类似于 Shell 中的 path，只有在指定目录下的文件才可以被自动执行。但是 Shell 中可以通过绝对路径来执行不在 path 目录中的程序，而 Python 没有办法这么做。

那么如何知道哪些路径下的 Python 文件能够被引入呢？这是在 sys.path 变量中定义的。

```

>>> import sys
>>> sys.path                           # 这是一个列表，每个元素表示一个搜索路径
['',                                            # 当前目录
'/anaconda3/lib/python37.zip',
'/anaconda3/lib/python3.7',
'/anaconda3/lib/python3.7/lib-dynload',
'/anaconda3/lib/python3.7/site-packages',
'/anaconda3/lib/python3.7/site-packages/aeosa']
```

可以看到，sys.path 是一个路径的列表，只要将 Python 文件放到其中任意一个路径下就可以被成功 import 进来。

> 这里要特别注意的是，第一个值是空的字符串，表示当前目录。这就是为何总是可以引入当前目录下的 Python 文件。还需要注意的是，这里的目录除了第一个之外，都是绝对路径。