# Python 函数返回多个值的方法（入门必读）

如果程序需要有多个返回值，则既可将多个值包装成列表之后返回，也可直接返回多个值。如果 Python 函数直接返回多个值，Python 会自动将多个返回值封装成元组。

如下程序示范了函数直接返回多个值的情形：

```
def sum_and_avg(list):
    sum = 0
    count = 0
    for e in list:
        # 如果元素 e 是数值
        if isinstance(e, int) or isinstance(e, float):
            count += 1
            sum += e
    return sum, sum / count
my_list = [20, 15, 2.8, 'a', 35, 5.9, -1.8]
# 获取 sum_and_avg 函数返回的多个值，多个返回值被封装成元组
tp = sum_and_avg(my_list) #①
print(tp)
```

上面程序中的第 9 行代码返回了多个值，当 ① 号代码调用该函数时，该函数返回的多个值将会被自动封装成元组，因此程序看到 tp 是一个包含两个元素（由于被调用函数返回了两个值）的元组。

此外，也可使用 Python 提供的序列解包功能，直接使用多个变量接收函数返回的多个值。例如如下代码：

```
# 使用序列解包来获取多个返回值
s, avg = sum_and_avg(my_list) #②
print(s)
print(avg)
```

上面程序中的 ② 号代码直接使用两个变量来接收 sum_and_avg() 函数返回的两个值，这就是利用了 Python 提供的序列解包功能。