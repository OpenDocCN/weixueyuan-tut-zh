# Python ++和--（自增和自减）

> 原文：[`www.weixueyuan.net/a/367.html`](http://www.weixueyuan.net/a/367.html)

和 C 语言及 Java 语言不同，在 Python 中没有自增`++`和自减`--`操作符。

## 自减 --

在《Python 整数类型及其运算》中我们介绍过`-`操作符，表示得到相反数。如果是两个`-`则表示取了相反数后再次取相反数，所以 --a 表示取 a 的相反数的相反数，得到的结果为 a。

```
>>> a = 12
>>> --a     # a 的相反数的相反数
12
```

但是如果我们仿造 C 语言，将“--”放到变量的后面，将会导致语法错误。

```
>>> a = 12
>>> a--                       # 在 Python 中 a--是不合法的语法
File "<stdin>", line 1        # 抛出异常
  a--
    ^
SyntaxError: invalid syntax   # 语法错误
```

## 自增 ++

那么还有一个问题，是否存在 ++a 呢？其实这种写法是允许的。在 Python 中，+a 就表示 a， ++a 就表示 +(+a)=+a=a，即 ++a 其实就是取 a 的值。

```
>>> a = 12
>>> +a      # 在 Python 中+a 就表示 a
12
>>> ++a     # ++a = +(+a) = a
12
```

同样的情形，Python 中不支持 a++的语法。

```
>>> a = 12
>>> a++                  # 在 Python 中 a++是不合法的语法
File "<stdin>", line 1   # 抛出异常
  a++
    ^
SyntaxError: invalid syntax
```