# Python closed()函数：关闭打开的文件

> 原文：[`www.weixueyuan.net/a/817.html`](http://www.weixueyuan.net/a/817.html)

在文件使用完毕后，需要关闭文件以释放资源。关闭文件的方法是：

文件对象.close()

可以对一个已经关闭的文件再次执行 close() 操作，不必担心会产生异常。

在文件关闭后便不能对其进行读写操作。文件关闭后，文件对象还是存在的，但其属性 closed 现在为 True，表示该文件已经被关闭了。

> 文件关闭后，如果尝试对其进行读写操作，则会抛出 ValueError 异常。

文件关闭后，除了 closed 外，文件的其他属性并不会发生变化，如 name、mode 等属性还是有效的，能够被继续使用。

```

>>> fd = open("in.dat", "r")   # 打开文件
>>> fd.closed                                  # closed 的值为 False，表示没有关闭
False
>>> fd.close()                                 # 关闭该文件
>>> fd.closed                                  # 现在 closed 的值为 True
True
>>> fd.read()                                  # 如果试图进行读操作，则抛出异常
Traceback (most recent call last):
   File "<stdin>", line 1, in <module>
ValueError: I/O operation on closed file.
>>> fd.name                                    # 查看 name 属性，依然有效
'in.dat'
>>> fd.mode                                    # 查看 mode 属性，依然有效
'r'
```