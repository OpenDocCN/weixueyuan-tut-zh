# Python 布尔类型和字符串的相互转换

> 原文：[`www.weixueyuan.net/a/384.html`](http://www.weixueyuan.net/a/384.html)

在 Python 中，布尔类型和字符串是可以相互转换的。

#### 1) 将字符串转换成布尔类型

将其他类型转换成布尔类型的最简单办法是使用 bool() 函数。如将字符串转换成布尔类型，其规则是如果字符串为空字符串，就是不包含任何字符、长度为 0 的字符串，转换成布尔类型时为 False，其他都转换成 True，包括“False”。

请看下面的例子：

```

>>> bool("")       # 空字符串
False
>>> bool("False")  # 非空字符串
True
>>> bool("123")
True
```

#### 2) 将布尔类型转换成字符串

如果想将布尔类型的转换成字符串，可以使用 str() 函数。其返回的值分别是“True”和“False”。

请看下面的例子：

```

>>> str(True)   # 将布尔类型的 True 转换成字符串类型
'True'
>>> str(False)  # 将布尔类型的 False 转换成字符串类型
'False'
```