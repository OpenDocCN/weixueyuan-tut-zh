# Python 循环结构中 else 用法（入门必读）

Python 的循环都可以定义 else 代码块，当循环条件为 False 时，程序会执行 else 代码块。

如下代码示范了为 while 循环定义 else 代码块：

```
count_i = 0
while count_i < 5:
    print('count_i 小于 5: ', count_i)
    count_i += 1
else:
    print('count_i 大于或等于 5: ', count_i)
```

运行上面程序，可以看到如下输出结果：

count_i 小于 5:  0
count_i 小于 5:  1
count_i 小于 5:  2
count_i 小于 5:  3
count_i 小于 5:  4
count_i 大于或等于 5:  5

从上面的运行过程来看，当循环条件 count i < 5 变成 False 时，程序执行了 while 循环的 else 代码块。

简单来说，程序在结束循环之前，会先执行 else 代码块。从这个角度来看，else 代码块其实没有太大的价值，将 else 代码块直接放在循环体之外即可。也就是说，上面的循环其实可改为如下形式：

```
count_i = 0
while count_i < 5:
    print('count_i 小于 5: ', count_i)
    count_i += 1
print('count_i 大于或等于 5: ', count_i)
```

上面代码直接将 else 代码块放在 while 循环体之外，程序执行结果与使用 else 代码块的执行结果完全相同。

循环的 else 代码块是 Python 的一个很特殊的语法（其他编程语言通常不支持），else 代码块的主要作用是便于生成更优雅的 Python 代码。

for 循环同样可使用 else 代码块，当 for 循环把区间、元组或列表的所有元素遍历一次之后， for 循环会执行 else 代码块，在 else 代码块中，循环计数器的值依然等于最后一个元素的值。例如如下代码：

```
a_list = [330, 1.4, 50, 'fkit', -3.5]
for ele in a_list:
    print('元素: ', ele)
else:
    # 访问循环计数器的值，依然等于最后一个元素的值
    print('else 块: ', ele)
```

运行上面程序，可以看到如下输出结果：

元素:  330
元素:  1.4
元素:  50
元素:  fkit
元素:  -3.5
else 块:  -3.5