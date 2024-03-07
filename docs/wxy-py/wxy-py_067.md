# Python fileno()函数：得到文件编号

> 原文：[`www.weixueyuan.net/a/816.html`](http://www.weixueyuan.net/a/816.html)

该函数用于得到文件在进程中的编号，这是一个整数值。其中，stdin 在进程中的文件编号永远是 0，stdout 永远是 1，stderr 永远是 2，其他文件的编号都大于 2。

下面的例子查看了普通文件的编号以及 3 个特殊文件的编号。

```

>>> import sys                    # 引入 sys 模块
>>> fd = open("./in.dat", "rb")   # 打开一个普通文件
>>> fd.fileno()               # 得到该文件的编号
3
>>> fd.close()                # 关闭该文件
>>> sys.stdin.fileno()        # 得到 stdin 的文件编号
0
>>> sys.stdout.fileno()       # 得到 stdout 的文件编号
1
>>> sys.stderr.fileno()       # 得到 stderr 的文件编号
2
```

如果该文件已经被关闭，则 fileno() 会抛出 ValueError 异常。下面的代码演示了这种情况。

```

>>> import sys
>>> fd = open("./in.dat", "rb")        # 打开文件
>>> fd.fileno()
3
>>> fd.close()       # 关闭文件
>>> fd.fileno()      # 无法得到文件编号，抛出异常 ValueError
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: I/O operation on closed file
```