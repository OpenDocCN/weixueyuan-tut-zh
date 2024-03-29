# Python bool 类型和比较运算符（入门必读）

Python 提供了 bool 类型来表示真（对）或假（错），比如常见的 `5 > 3` 比较算式，这个是正确的，在程序世界里称之为真（对），Python 使用 True 来代表；再比如 `4 > 20` 比较算式，这个是错误的，在程序世界里称之为假（错），Python 使用 False 来代表。

由此可见，bool 类型就是用于代表某个事情的真（对）或假（错），如果这个事情是正确的，用 True 代表；如果这个事情是错误的，用 False 代表。

比较运算符用于判断两个值（这两个值既可以是变量，也可以是常量，还可以是表达式）之间的大小，比较运算的结果是 bool 值（True 代表真，False 代表假）。

Python 支持的比较运算符如下：

*   >：大于，如果运算符前面的值大于后面的值，则返回 True；否则返回 False。
*   >=：大于或等于，如果运算符前面的值大于或等于后面的值，则返回 True；否则返回 False。
*   <：小于，如果运算符前面的值小于后面的值，则返回 True；否则返回 False。
*   <=：小于或等于，如果运算符前面的值小于或等于后面的值，则返回 True；否则返回 False。
*   ==：等于，如果运算符前面的值等于后面的值，则返回 True；否则返回 False。
*   !=：不等于，如果运算符前面的值不等于后面的值，则返回 True；否则返回 False。
*   is：判断两个变量所引用的对象是否相同，如果相同则返回 True。
*   is not：判断两个变量所引用的对象是否不相同，如果不相同则返回 True。

下面程序示范了比较运算符的使用：

```
# 输出 True
print("5 是否大于 4：", 5 > 4)
# 输出 False
print("3 的 4 次方是否大于等于 90.0：", 3 ** 4 >= 90)
# 输出 True
print("20 是否大于等于 20.0：", 20 >= 20.0)
# 输出 True
print("5 和 5.0 是否相等：", 5 == 5.0)
# 输出 False
print("True 和 False 是否相等：", True == False)
```

上面程序简单示范了 Python 比较运算符的功能和用法。

Python 的两个 bool 值分别是 True 和 False，但实际上 True 也可被当成整数 1 使用，False 也可被当成整数 0 使用。也就是说，True、False 两个值完全可以参与各种算术运算。例如如下代码：

```
# 输出 True
print("1 和 True 是否相等：", 1 == True)
# 输出 True
print("0 和 False 是否相等：", 0 == False)
print(True + False) # 输出 1
print(False - True)  # 输出-1
```

关于 == 与 is 看上去很相似，但实质上有区别，== 只比较两个变量的值，但 is 要求两个变量引用同一个对象。看如下代码：

```
import time
# 获取当前时间
a = time.gmtime()
b =  time.gmtime()
print(a == b) # a 和 b 两个时间相等，输出 True
print(a is b) # a 和 b 不是同一个对象，输出 False
```

上面代码中 a、b 两个变量都代表当前系统时间，因此 a、b 两个变量的时间值是相等的，故程序使用“==”判断返回 True。但由于 a、b 两个变量分别引用不同的对象（每次调用 gmtime() 函数都返回不同的对象），因此 `a is b` 返回 False。

实际上，Python 提供了一个全局的 id() 函数来判断变量所引用的对象的内存地址（相当于对象在计算机内存中存储位置的门牌号），如果两个对象所在的内存地址相同（相当于它们住在同一个房间内，计算机同一块内存在任一时刻只能存放一个对象），则说明这两个对象其实是同一个对象。

由此可见，is 判断其实就是要求通过 id() 函数计算两个对象时返回相同的地址。例如，使用 id() 函数判断上面的 a、b 两个变量：

print(id(a))
print(id(b))

从运行结果中将会看到 a、b 两个变量所引用的对象的内存地址是不同的，这样通过 is 来判断 a、b 两个变量自然也就输出 False 了。