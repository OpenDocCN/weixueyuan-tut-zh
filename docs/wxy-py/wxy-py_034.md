# Python str 和 bytes 的转换

> 原文：[`www.weixueyuan.net/a/427.html`](http://www.weixueyuan.net/a/427.html)

在 Python 3 中同时支持 str 类型和 bytes 两种类型，它们之间是可以相互转换的。如从 str 转换成 bytes，可以使用 encode() 成员函数。

```
>>> a = "abc"
>>> a
'abc'
>>> b = a.encode("utf-8")
>>> type(b)
<class 'bytes'>
```

下面的代码说明了带有中文的 str 类型是如何转换成 bytes 类型的。

```
>>> a = "最爱中国"         # str 类型
>>> len(a)                 # 长度为 4，4 个字符
4
>>> type(a)                # 类型
<class 'str'>
>>> b = a.encode("utf-8")  # 将其转换成 str 类型，方式是 utf-8
>>> type(b)
<class 'bytes'>          
>>> len(b)                 # 字节个数为 12，每个中文在 utf-8 中用 3 个字节表示
12
>>> b                      # 各个字节的内容
b'\xe6\x9c\x80\xe7\x88\xb1\xe4\xb8\xad\xe5\x9b\xbd'
```

从 bytes 类型转换成 str 类型可以使用成员函数 decode()，下面的代码说明了普通的拉丁字母是如何进行转换的。

```
>>> b = b"abc"
>>> type(b)
<class 'bytes'>
>>> len(b)
3
>>> a = b.decode("utf-8")
>>> type(a)
<class 'str'>
>>> len(a)
3
>>> a
'abc'
```

下面的代码说明了成员函数 decode() 如何将 bytes 类型转换成含中文字符的 str 类型。

```
>>> b = b'\xe6\x9c\x80\xe7\x88\xb1\xe4\xb8\xad\xe5\x9b\xbd'
>>> len(b)                 # 字节个数
12
>>> type(b)
< class 'bytes'>
>>> b
b'\xe6\x9c\x80\xe7\x88\xb1\xe4\xb8\xad\xe5\x9b\xbd'
>>> a = b.decode("utf-8")  # 按照 utf-8 的格式进行解码，每 3 个字节表示一个中文字符
>>> type(a)
<class 'str'>
>>> len(a)                 # 字符个素
4
>>> a
'最爱中国'
```