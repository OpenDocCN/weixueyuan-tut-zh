# Python 文件对象的属性

> 原文：[`www.weixueyuan.net/a/624.html`](http://www.weixueyuan.net/a/624.html)

文件对象也提供很多属性，如文件名、当前位置等信息。本节将介绍文件对象常用的属性。

#### 1) closed：文件是否关闭

closed 是一个布尔类型的属性，表示该文件是否被关闭。下面的例子演示了该属性的变化情况。如果正常打开文件，该值为 False，调用 close() 后该属性值变为 True。

可以使用该属性来判断某个文件对象是否还有效。

```

>>> fd = open("in.dat", "r")   # 打开文件
>>> fd.closed                  # closed 的值为 False，表示没有关闭
False
>>> fd.close()                 # 关闭该文件
>>> fd.closed                  # 现在 closed 的值为 True
True
```

#### 2) name：文件名

name 就是打开的文件名称，是一个字符串类型的属性。

```

>>> fd = open("in.dat", "rb")  # 打开文件
>>> fd.name                    # 查看文件名
'in.dat'
>>> fd.close()                 # 关闭文件
```

需要特别注意的是，sys.stdin、sys.stdout 和 sys.stderr 这些特殊文件的名称是比较独特的。下面的例子演示了这些特殊文件的名称。

```

>>> import sys          # 引入 sys 模块
>>> sys.stdin.name      # 显示标准输入文件的名称
'<stdin>'
>>> sys.stdout.name     # 显示标准输出文件的名称
'<stdout>'
>>> sys.stderr.name     # 显示标准错误输出文件的名称
'<stderr>'
```

#### 3) mode：打开属性

mode 即文件打开的模式，如只读、只写、添加等。该属性也是字符串类型。

#### 4) encoding：编码方式

encoding 是一个字符串，表示该文件的编码方式。其用法在前面章节中已经介绍过。