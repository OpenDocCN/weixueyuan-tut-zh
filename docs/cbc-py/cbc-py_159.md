# Python readline 和 readlines 函数：按行读取文件

如果程序要读取行，通常只能用文本方式来读取，道理很简单，只有文本文件才有行的概念，二进制文件没有所谓行的概念。

文件对象提供了如下两个方法来读取行：

*   readline([n])：读取一行内容。如果指定了参数 n，则只读取此行内的 n 个字符。
*   readlines()：读取文件内所有行。

下面程序示范了使用 readline() 方法来读取文件内容：

```
import codecs
# 指定使用 utf-8 字符集读取文件内容
f = codecs.open("readline_test.py", 'r', 'utf-8', buffering=True)
while True:
    # 每次读取一行
    line = f.readline()
    # 如果没有读到数据，跳出循环
    if not line: break
    # 输出 line
    print(line, end='')
f.close()
```

上面程序使用 UTF-8 字符集打开 readline_test.py 文件，这是由于该 Python 源文件是采用 UTF-8 字符集保存的，因此，如果直接用普通的 open() 函数打开文件，则会引发 UnicodeDecodeError 异常。

接下来程序使用 readline() 方法逐行进行读取，当读取到结尾时，该方法将会返回空，程序就会退出循环。

程序也可以使用 readlines() 方法一次读取文件内所有行。例如如下程序：

```
import codecs
# 指定使用 utf-8 字符集读取文件内容
f = codecs.open("readlines_test.py", 'r', 'utf-8', buffering=True)
# 使用 readlines()读取所有行，返回所有行组成的列表
for 1 in f.readlines():
    print(1, end='')
f.close()
```