# Python and、or 和 not（逻辑运算）

> 原文：[`www.weixueyuan.net/a/381.html`](http://www.weixueyuan.net/a/381.html)

在 Python 中，除了常规的整数操作外，布尔类型有其独特的运算，通常称为逻辑运算。

## 1\. 与（and）

该操作符有两个操作数，要求这两个操作数都是布尔型的。如果两个操作数都是 True，那么结果是 True；否则就是 False。

表 1 所示为其运算规则。

表 1：与运算规则

| A | B | A and B |
| True | True | True |
| True | False | False |
| False | True | False |
| False | False | False |

下面是实际操作的情况：

```

>>> True and True     # 两个操作数都是 True
True
>>> True and False    # 两个操作数一个是 True，另外一个是 False
False
>>> False and True    # 两个操作数一个是 True，另外一个是 False
False
>>> False and False   # 两个操作数都是 False
False
```

## 2\. 或（or）

该操作符也需要两个操作数，而且这两个操作数都应该是布尔类型的。如果有一个操作数的值是 True，那么运算结果就是 True；否则结果是 False。

表 2 所示为其运算规则。

表 2：或运算规则

| A | B | A or B |
| True | True | True |
| True | False | True |
| False | True | True |
| False | False | False |

下面是实际操作的情况：

```

>>> True or True   # 演示布尔类型的 or 运算
True
>>> True or False
True
>>> False or True
True
>>> False or False
False
```

## 3\. 非（not）

该操作符需要一个操作数，要求操作数是布尔类型的。如果操作数是 True，那么结果是 False；如果操作数的值为 False，那么结果就是 True。

表 3 所示为其运算规则。

表 3：非运算规则

| A | not A |
| True | False |
| False | True |