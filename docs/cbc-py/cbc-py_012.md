# Python bytes 类型及用法

Python 3 新增了 bytes 类型，用于代表字节串（这是作者生造的一个词，与字符串对应）。字符串（str）由多个字符组成，以字符为单位进行操作；字节串（bytes）由多个字节组成，以字节为单位进行操作。

bytes 和 str 除操作的数据单元不同之外，它们支持的所有方法都基本相同，bytes 也是不可变序列。

bytes 对象只负责以字节（二进制格式）序列来记录数据，至于这些数据到底表示什么内容，完全由程序决定。如果采用合适的字符集，字符串可以转换成字节串；反过来，字节串也可以恢复成对应的字符串。

由于 bytes 保存的就是原始的字节（二进制格式）数据，因此 bytes 对象可用于在网络上传输数据，也可用于存储各种二进制格式的文件，比如图片、音乐等文件。

如果希望将一个字符串转换成 bytes 对象，有如下三种方式：

1.  如果字符串内容都是 ASCII 字符，则可以通过直接在字符串之前添加 b 来构建字节串值。
2.  调用 bytes() 函数（其实是 bytes 的构造方法）将字符串按指定字符集转换成字节串，如果不指定字符集，默认使用 UTF-8 字符集。
3.  调用字符串本身的 encode() 方法将字符串按指定字符集转换成字节串，如果不指定字符集，默认使用 UTF-8 字符集。

例如，如下程序示范了如何创建字节串：

```
# 创建一个空的 bytes
b1 = bytes()
# 创建一个空的 bytes 值
b2 = b''
# 通过 b 前缀指定 hello 是 bytes 类型的值
b3 = b'hello'
print(b3)
print(b3[0])
print(b3[2:4])
# 调用 bytes 方法将字符串转成 bytes 对象
b4 = bytes('我爱 Python 编程',encoding='utf-8')
print(b4)
# 利用字符串的 encode()方法编码成 bytes，默认使用 utf-8 字符集
b5 = "学习 Python 很有趣".encode('utf-8')
print(b5)
```

上面程序中 b1～b5 都是字节串对象，该程序示范了以不同方式来构建字节串对象。其中 b2、b3 都是直接在 ASCII 字符串前添加 b 前缀来得到字节串的：b4 调用 bytes() 函数来构建字节串；而 b5 则调用字符串的 encode 方法来构建字节串。

运行上面程序，可以看到如下输出结果：

b'hello'
104
b'll'
b'\xe6\x88\x91\xe7\x88\xb1Python\xe7\xbc\x96\xe7\xa8\x8b'
b'\xe5\xad\xa6\xe4\xb9\xa0Python\xe5\xbe\x88\xe6\x9c\x89\xe8\xb6\xa3'

从上面的输出结果可以看出，字节串和字符串非常相似，只是字节串里的每个数据单元都是 1 字节。

计算机底层有两个基本概念：位（bit）和字节（Byte），其中 bit 代表 1 位，要么是 0，要么是 1，就像一盏灯，要么打开，要么熄灭；Byte 代表 1 字节，1 字节包含 8 位。

在字节串中每个数据单元都是字节，也就是 8 位，其中每 4 位（相当于 4 位二进制数，最小值为 0 ，最大值为 15）可以用一个十六进制数来表示，因此每字节需要两个十六进制数表示，所以可以看到上面的输出是 b'\xe6\x88\x91\xe7\x88\xb1Python\xe7\xbc\x96\xe7\xa8\x8b'，比如 \xe6 就表示 1 字节，其中 \x 表示十六进制，e6 就是两位的十六进制数。

如果程序获得了 bytes 对象，也可调用 bytes 对象的 decode() 方法将其解码成字符串，例如，在上面程序中添加如下代码：

```
#将 bytes 对象解码成字符串，默认使用 UTF-8 进行解码
st = b5.decode('utf-8')
print(st)#学习 Python 很有趣
```

运行上面程序，可以看到如下输出结果：

学习 Python 很有趣

这里简单介绍一下字符集的概念。计算机底层并不能保存字符，但程序总是需要保存各种字符的，那该怎么办呢？计算机“科学家”就想了一个办法：为每个字符编号，当程序要保存字符时，实际上保存的是该字符的编号；当程序读取字符时，读取的其实也是编号，接下来要去查“编号一字符对应表”（简称码表）才能得到实际的字符。

因此，所谓的字符集，就是所有字符的编号组成的总和。早期美国人给英文字符、数字、标点符号等字符进行了编号，他们认为所有字符加起来顶多 100 多个，只要 1 字节（8 位，支持 256 个字符编号）即可为所有字符编号一一这就是 ASCII 字符集。

后来，亚洲国家纷纷为本国文字进行编号，即制订本国的字符集，但这些字符集并不兼容。于是美国人又为世界上所有书面语言的字符进行了统一编号，这次他们用了两个字节(16 位，支持 65536 个字符编号），这就是 Unicode 字符集。实际使用的 UTF-8, UTF-16 等其实都属于 Unicode 字符集。

由于不同人对字符的编号完全可以很随意，比如同一个“爱”字，我可以为其编号为 99，你可以为其编号为 199，所以同一个编号在不同字符集中代表的字符完全有可能是不同的。因此，对于同一个字符串，如果采用不同的字符集来生成 bytes 对象，就会得到不同的 bytes 对象。