# Python fileinput 模块：逐行读取多个文件

fileinput 模块提供了如下函数可以把多个输入流合并在一起：

*   fileinput.input（files=None, inplace=False, backup='', bufsize=0, mode='r', openhook=None）：该函数中的 files 参数用于指定多个文件输入流。该函数返回一个 FileInput 对象。

当程序使用上面函数创建了 FileInput 对象之后，即可通过 for 循环来遍历文件的每一行。此外，fileinput 还提供了如下全局函数来判断正在读取的文件信息：

*   fileinput.filename()：返回正在读取的文件的文件名。
*   fileinput.fileno()：返回当前文件的文件描述符（file descriptor），该文件描述符是一个整数。

    文件描述符是一个文件的代号，其值为一个整数。后续章节将会介绍关于文件描述符的操作。

*   fileinput.lineno()：返回当前读取的行号。
*   fileinput.filelineno()：返回当前读取的行在其文件中的行号。
*   fileinput.isfirstline()：返回当前读取的行在其文件中是否为第一行。
*   fileinput.isstdin()：返回最后一行是否从 sys.stdin 读取。程序可以使用“-”代表从 sys.stdin 读取。
*   fileinput.nextfile()：关闭当前文件，开始读取下一个文件。
*   fileinput.close()：关闭 FileInput 对象。

通过上面的介绍不难发现，fileinput 也存在一个缺陷，即在创建 FileInput 对象时不能指定字符集，因此它所读取的文件的字符集必须与操作系统默认的字符集保持一致。当然，如果文本文件的内容是纯英文，则不存在字符集的问题。

下面程序示范了使用 fileinput 模块来读取多个文件：

```
import fileinput
# 一次读取多个文件
for line in fileinput.input(files=('info.txt', 'test.txt')):
    # 输出文件名，当前行在当前文件中的行号
    print(fileinput.filename(), fileinput.filelineno(), line, end='')
# 关闭文件流
fileinput.close()
```

上面程序使用 fileinput.input 直接合并了 info.txt 和 test.txt 两个文件，这样程序可以直接遍历读取这两个文件的内容。