# Python 逻辑运算符及其用法

逻辑运算符用于操作 bool 类型的变量、常量或表达式，逻辑运算的返回值也是 bool 值。

Python 的逻辑运算符有如下三个：

1.  and：与，前后两个操作数必须都是 True 才返回 True；否则返回 False。
2.  or：或，只要两个操作数中有一个是 True，就可以运回 True；否则返回 False；
3.  not：非，只需要一个操作数，如果操作数为 True，则返回 False；如果操作数为 False，则返回 True。

下面代码示范了与、或、非这三个逻辑运算符的使用：

```
# 直接对 False 求非运算，将返回 True
print(not False)
# 5>3 返回 True，20.0 大于 10，因此结果返回 True
print(5 > 3 and 20.0 > 10)
# 4>=5 返回 False，"c">"a"返回 True。求或后返回 True
print(4 >= 5 or "c" > "a")
```

有些时候，程序需要使用多个逻辑运算符来组合复杂的逻辑。例如，假设想表达如下逻辑：需要购买一套必须以“Python”结尾的教程，且教程价格小于 50 元或该教程是基于“正式版”的。假如使用如下程序：

```
bookName = "C 语言中文网 Python"
price = 99
version = "正式版"
if bookName.endswith('Python') and price < 50 or version == "正式版" :
    print("打算购买这套 Python 教程")
else:
    print("不购买！")
```

编译、运行上面程序，可以看到程序输出“打算购买这套 Python 教程”。那么上面程序是否有问题呢？

其实问题是存在的，这是因为程序会先计算 `bookName.endswith('Python') and price < 50`，即使该逻辑表达式中的两个条件都是 False，但只要后面的 `version==“正式版”` 返回 True，整个表达式就会返回 True，从而导致程序依然会输出“打算购买这套 Python 教程”。因此，即使把上面程序中的 bookName 改为不以“Python”结尾，程序也依然会输出“打算购买这套 Python 教程”。

运算结果显然与逻辑需求并不一致，逻辑需求是：需要购买一套名以“Python” 结尾的教程。此时应该使用圆括号来保证程序先对 `price < 50 || version =="正式版"` 求值，然后再与 `bookName.endswith('Python')` 的结果求与。

因此，应该把程序改为如下形式：

```
bookName = "C 语言中文网 Python"
price = 99
version = "正式版"
if bookName.endswith('Python') and (price < 50 or version == "正式版") :
    print("打算购买这套 Python 教程")
else:
    print("不购买！")
```

从上面代码可以看出，对于组合逻辑来说，使用圆括号保证运算顺序非常重要。

需要说明的是，即使不是为了保证逻辑运算的顺序，且有括号和没括号的输出结果是一样的，本教程也依然建议使用圆括号来提高程序的可读性。