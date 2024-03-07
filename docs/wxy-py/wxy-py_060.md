# Python open()：打开文件

> 原文：[`www.weixueyuan.net/a/808.html`](http://www.weixueyuan.net/a/808.html)

打开文件可以使用“open（文件名，模式）”来完成。该函数有两个参数，第一个参数是文件名，第二个参数是打开的模式。该函数返回一个文件对象，以后的读写都需要使用该文件对象。

文件名可以包含路径，如 cfg/config.ini。使用绝对路径和相对路径都是允许的，但不能使用通配符，如 log/*.log。对于 Windows 用户来说，由于使用 \ 作为路径分隔符，而有人喜欢用两个连续的反斜杠 \ 即 \\ 来表示 \，如"c:\\log\\app.log"。推荐的用法是在引号前面加上 r，这样看起来更加自然一点，如 r"c:\log\app.log"。

> 模式参数也是一个字符串，通过不同的模式值可以指定按照只读、只写、尾部添加等方式来打开某个文件。

模式的基本格式如下：

mode  = 打开方式 + 文件类型

文件类型包括两种，文本模式和二进制模式。文本模式用 t 表示，这是默认值；二进制模式用 b 表示。

打开方式包括：只读，用 r 表示；只写，用 w 表示；添加，用 a 表示。除此之外，还包括可读可写，用 w+ 或者 r+ 表示。它们的区别是 w+ 会清空原来的文件内容，而 r+ 不会清空原来的文件内容。如 rb 即表示用二进制模式 + 只读打开指定文件。

如果操作的文件不存在或者没有权限进行相关操作时，其会抛出 FileNotFoundError 或者 IOError 异常，此时一般需要使用 try 语句来处理。下面是在 Python 3 中打开不存在的文件的情况：

```

>>> fd = open("notExists.dat", "r")    # 打开不存在的文件，抛出异常
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
FileNotFoundError: [Errno 2] No such file or directory: 'notExists.dat'
```

下面是在 Python 2 中打开不存在的文件的情况：

```

>>> fd = open("notExists.dat", "r")    # Python 2 中的异常情况
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IOError: [Errno 2] No such file or directory: 'notExists.dat'
```

为了处理这些打开文件的异常情况，可以使用下面的代码：

```

try:
    file_obj1 = open(u"nonexist.txt", "r")
    file_obj1.close()
except IOError:
    # 找不到文件时提示文件不存在
    print(u"File not Exist")
```

如果希望某个文件只在某个局部有效，可以使用 with 语句。下面是一个使用的例子：

```

>>> with open('in.dat', 'r') as f:  # 使用 with 语句，f 是打开的文件对象
...     print(f.read())    # 读出所有的数据并打印到屏幕上
...                                                                     # 结束 with 语句
this is input text file   # 文件内容
it contains 3 lines
this is the end of file
>>> f                     # 查看 f
<_io.TextIOWrapper name='in.dat' mode='r' encoding='UTF-8'>
>>> f.closed              # f 是不是自动关闭了？
True              # 文件对象自动关闭了
```