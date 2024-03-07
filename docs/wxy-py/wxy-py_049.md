# Python 包的使用

> 原文：[`www.weixueyuan.net/a/618.html`](http://www.weixueyuan.net/a/618.html)

包对应的是一个目录而不是一个源文件，包目录下一定要存在一个名为 __init__.py 的文件。如我们可以创建下面的目录结构：

$ tree pkgDemo1/
pkgDemo1/
└── __init__.py

这里 pkgDemo1 目录就是一个包。

```

>>> import pkgDemo1
>>> pkgDemo1
<module 'pkgDemo1' from '/pkgDemo1/__init__.py'>
```

__init__.py 现在就是一个模块，可以使用前面介绍的方法来使用其定义的内容。如修改 __init__.py 的内容，让其包含以下内容：

```

""" this is the pakage demo file
"""
name =  "pkgDemo1"
author = "love.pythoh"
```

现在就可以访问这些新加的内容了：

```

>>> import pkgDemo1
>>> pkgDemo1.name
'pkgDemo1'
>>> pkgDemo1.author
'love.pythoh'
>>> pkgDemo1.__doc__
' this is the pakage demo file\n'
```

一般来说，一个包包含多个模块。例如在同一个目录下放入两个文件 mod1.py 和 mod2.py，它们的结构如下：

```

$ tree pkgDemo1/
pkgDemo1/
├── __init__.py
├── mod1.py
└── mod2.py
```

mod1.py 的内容如下：

```

""" this is mod1.py
"""
name =  "pkgDemo1.mod1"
def hello():
    print("hello from pkgDemo1.mod1")
```

mod2.py 的内容如下：

```

""" this is mod2.py
"""
name =  "pkgDemo1.mod2"
def hello():
def hello():
    print("hello from pkgDemo1.mod2")
```

下面来使用这个包的内容，代码如下：

```

>>> import pkgDemo1.mod1        # 引入 mod1，注意前面的包名
>>> import pkgDemo1.mod2        # 引入 mod2，注意前面的包名
>>> pkgDemo1.mod1
<module 'pkgDemo1.mod1' from 'pkgDemo1/mod1.py'>
>>> pkgDemo1.mod2
<module 'pkgDemo1.mod2' from '/pkgDemo1/mod2.py'>
>>> pkgDemo1.mod1.hello()       # 带上包名和模块名
hello from pkgDemo1.mod1
>>> pkgDemo1.mod2.hello()
hello from pkgDemo1.mod2
```