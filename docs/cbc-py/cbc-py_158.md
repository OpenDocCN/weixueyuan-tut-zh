# Python read 函数：按字节（字符）读取文件

文件对象提供了 read() 方法来按字节或字符读取文件内容，到底是读取宇节还是字符，则取决于是否使用了 b 模式，如果使用了 b 模式，则每次读取一个字节；如果没有使用 b 模式，则每次读取一个字符。在调用该方法时可传入一个整数作为参数，用于指定最多读取多少个字节或宇符。

例如，如下程序采用循环读取整个文件的内容：

```
f = open("read_test.py", 'r', True)
while True:
    # 每次读取一个字符
    ch = f.read(1)
    # 如果没有读到数据，跳出循环
    if not ch: break
    # 输出 ch
    print(ch, end='')
f.close()
```

上面程序采用循环依次读取每一个字符（因为程序没有使用 b 模式），每读取到一个字符，程序就输出该字符。

正如从上面程序所看到的，当程序读写完文件之后，推荐立即调用 close() 方法来关闭文件，这样可以避免资源泄露。如果需要更安全地关闭文件，推荐将关闭文件的 close() 方法调用在 finally 块中执行。例如，将上面程序改为如下形式：

```
f =open ("test.txt",'r',True)
try:
    while true:
        #每次读取一个字符
        ch = f.read(1)
        #如果没有读取到数据，则跳出循环
        if not ch:break
        #输出 ch
        print(ch, end='')
    finally:
        f.close() 
```

本章为了突出主题，简化程序，都将直接调用 close() 方法关闭文件，避免使用 finally 块。

如果在调用 read() 方法时不传入参数，该方法默认会读取全部文件内容。例如如下程序：

```
f = open("test.txt", 'r', True)
# 直接读取全部文件
print(f.read())
f.close()
```

通过上面两个程序，读者可能已经发现了一个问题，当使用 open() 函数打开文本文件时，程序使用的是哪种字符集呢？总是使用当前操作系统的字符集，比如 Windows 平台，open() 函数总是使用 GBK 字符集。因此，上面程序读取的 test.txt 也必须使用 GBK 字符集保存；否则，程序就会出现 UnicodeDecodeError 错误。

如果要读取的文件所使用的字符集和当前操作系统的字符集不匹配，则有两种解决方式：

1.  使用二进制模式读取，然后用 bytes 的 decode() 方法恢复成字符串。
2.  利用 codecs 模块的 open() 函数来打开文件，该函数在打开文件时允许指定字符集。

下面程序使用二进制模式来读取文本文件：

```
# 指定使用二进制方式读取文件内容
f = open("read_test3.py", 'rb', True)
# 直接读取全部文件，并调用 bytes 的 decode 将字节内容恢复成字符串
print(f.read().decode('utf-8'))
f.close()
```

上面程序在调用 open() 函数时，传入了 rb 模式，这表明采用二进制模式读取文件，此时文件对象的 read() 方法返回的是 bytes 对象，程序可调用 bytes 对象的 decode() 方法将它恢复成字符串。由于此时读取的 read_test3.py 文件是以 UTF-8 的格式保存的，因此程序需要使用 decode() 方法恢复字符串时显式指定使用 UTF-8 字符集。

下面程序使用 codes 模块的 open() 函数来打开文件，此时可以显式指定字符集：

```
import codecs
#指定使用 utf-8 字符集读取文件内容
f = codecs.open("read_test4.py", 'r', 'utf-8', buffering=True)
while True:
    #每次读取一个字符
    ch = f.read(1)
    #如果没有读取到数据，则跳出循环
    if not ch : break
    #输出 ch
    print (ch, end='')
f.close()
```

上面程序在调用 open() 函数时显式指定使用 UTF-8 字符集，这样程序在读取文件内容时就完全没有问题了。