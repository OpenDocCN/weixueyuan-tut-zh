# Python raise 用法（超级详细，看了无师自通）

当程序出现错误时，系统会自动引发异常。除此之外，Python 也允许程序自行引发异常，自行引发异常使用 raise 语句来完成。

异常是一种很“主观”的说法，以下雨为例，假设大家约好明天去爬山郊游，如果第二天下雨了，这种情况会打破既定计划，就属于一种异常；但对于正在期盼天降甘霖的农民而言，如果第二天下雨了，他们正好随雨追肥，这就完全正常。

很多时候，系统是否要引发异常，可能需要根据应用的业务需求来决定，如果程序中的数据、执行与既定的业务需求不符，这就是一种异常。由于与业务需求不符而产生的异常，必须由程序员来决定引发，系统无法引发这种异常。

如果需要在程序中自行引发异常，则应使用 raise 语句。raise 语句有如下三种常用的用法：

1.  raise：单独一个 raise。该语句引发当前上下文中捕获的异常（比如在 except 块中），或默认引发 RuntimeError 异常。
2.  raise 异常类：raise 后带一个异常类。该语句引发指定异常类的默认实例。
3.  raise 异常对象：引发指定的异常对象。

上面三种用法最终都是要引发一个异常实例（即使指定的是异常类，实际上也是引发该类的默认实例），raise 语句每次只能引发一个异常实例。

可以利用 raise 语句再次改写前面五子棋游戏中处理用户输入的代码：

```
try :
    #将用户输入的字符串以逗号（ ，）作为分隔符，分隔成两个字符串
    x_str, y_str = inputStr.split(sep =",")
    #如果要下棋的点不为空
    if board[int(y_str) - 1] [int(x_str) - 1] != "+":
    #引发默认的 RuntimeError 异常
    raise
    #把对应的列表元素赋为”●”
    board [int(y_str) - 1] [int(x_str) - 1] = ”●”
except Exception as e:
    print (type(e))
    inputStr = input("您输入的坐标不合法，请重新输入，下棋坐标应以 x,y 的格式\n")
    continue
```

上面程序中第 7 行代码使用 raise 语句来自行引发异常，程序认为当用户试图向一个已有棋子的坐标点下棋时就是异常。当 Python 解释器接收到开发者自行引发的异常时，同样会中止当前的执行流，跳到该异常对应的 except 块，由该 except 块来处理该异常。也就是说，不管是系统自动引发的异常，还是程序员于动引发的异常，Python 解释器对异常的处理没有任何差别。

即使是用户自行引发的异常，也可以使用 `try except` 来捕获它。当然也可以不管它，让该异常向上（先调用者）传播，如果该异常传到 Python 解释器，那么程序就会中止。

下面示例示范了处理用户引发异常的两种方式：

```
def main():
    try:
        # 使用 try...except 来捕捉异常
        # 此时即使程序出现异常，也不会传播给 main 函数
        mtd(3)
    except Exception as e:
        print('程序出现异常:', e)
    # 不使用 try...except 捕捉异常，异常会传播出来导致程序中止
    mtd(3)
def mtd(a):
    if a > 0:
        raise ValueError("a 的值大于 0，不符合要求")
main()
```

从上面程序可以看到，程序既可在调用 mtd(3) 时使用 `try except` 来捕获异常，这样该异常将会被 except 块捕获，不会传播给调用它的函数；也可直接调用 mtd(3)，这样该函数的异常就会直接传播给它的调用函数，如果该函数也不处理该异常，就会导致程序中止。

运行上面程序，可以看到如下输出结果：

程序出现异常: a 的值大于 0，不符合要求
Traceback (most recent call last):
  File "C:\Users\mengma\Desktop\1.py", line 13, in <module>
    main()
  File "C:\Users\mengma\Desktop\1.py", line 9, in main
    mtd(3)
  File "C:\Users\mengma\Desktop\1.py", line 12, in mtd
    raise ValueError("a 的值大于 0，不符合要求")
ValueError: a 的值大于 0，不符合要求

上面第一行输出是第一次调用 mtd (3) 的结果，该方法引发的异常被 except 块捕获并处理。后面的大段输出则是第二次调用 mtd(3) 的结果，由于该异常没有被 except 块捕获，因此该异常一直向上传播，直到传给 Python 解释器导致程序中止。

第二次调用 mtd(3) 引发的以“File”开头的三行输出，其实显示的就是异常的传播轨迹信息。也就是说，如果程序不对异常进行处理，Python 默认会在控制台输出异常的传播轨迹信息。

## 自定义异常类

很多时候，程序可选择引发自定义异常，因为异常的类名通常也包含了该异常的有用信息。所以在引发异常时，应该选择合适的异常类，从而可以明确地描述该异常情况。在这种情形下，应用程序常常需要引发自定义异常。

用户自定义异常都应该继承 Exception 基类或 Exception 的子类，在自定义异常类时基本不需要书写更多的代码，只要指定自定义异常类的父类即可。

下面程序创建了一个自定义异常类（程序一）：

class AuctionException(Exception): pass

上面程序创建了 AuctionException 异常类，该异常类不需要类体定义，因此使用 pass 语句作为占位符即可。

在大部分情况下，创建自定义异常类都可采用与程序一相似的代码来完成，只需改变 AuctionException 异常的类名即可，让该异常的类名可以准确地描述该异常。

## except 和 raise 同时使用

在实际应用中对异常可能需要更复杂的处理方式。当一个异常出现时，单靠某个方法无法完全处理该异常，必须由几个方法协作才可完全处理该异常。也就是说，在异常出现的当前方法中，程序只对异常进行部分处理，还有些处理需要在该方法的调用者中才能完成，所以应该再次引发异常，让该方法的调用者也能捕获到异常。

为了实现这种通过多个方法协作处理同一个异常的情形，可以在 except 块中结合 raise 语句来完成。如下程序示范了 except 和 raise 同时使用的方法：

```
class AuctionException(Exception): pass
class AuctionTest:
    def __init__(self, init_price):
        self.init_price = init_price
    def bid(self, bid_price):
        d = 0.0
        try:
            d = float(bid_price)
        except Exception as e:
            # 此处只是简单地打印异常信息
            print("转换出异常：", e)
            # 再次引发自定义异常
            raise AuctionException("竞拍价必须是数值，不能包含其他字符！")  # ①
            raise AuctionException(e)
        if self.init_price > d:
            raise AuctionException("竞拍价比起拍价低，不允许竞拍！")
        initPrice = d
def main():
    at = AuctionTest(20.4)
    try:
        at.bid("df")
    except AuctionException as ae:
        # 再次捕获到 bid()方法中的异常，并对该异常进行处理
        print('main 函数捕捉的异常：', ae)
main()
```

上面程序中 9~13 行代码对应的 except 块捕获到异常后，系统打印了该异常的字符串信息，接着引发一个 AuctionException 异常，通知该方法的调用者再次处理该 AuctionException 异常。所以程序中的 main() 函数，也就是 bid() 方法的调用者还可以再次捕获 AuctionException 异常，井将该异常的详细描述信息打印出来。

这种 except 和 raise 结合使用的情况在实际应用中非常常用。实际应用对异常的处理通常分成两个部分：

1.  应用后台需要通过日志来记录异常发生的详细情况；
2.  应用还需要根据异常向应用使用者传达某种提示；

在这种情形下，所有异常都需要两个方法共同完成，也就必须将 except 和 raise 结合使用。

如果程序需要将原始异常的详细信息直接传播出去，Python 也允许用自定义异常对原始异常进行包装，只要将上面 ① 号代码改为如下形式即可：

raise AuctionException(e)

上面就是把原始异常 e 包装成了 AuctionException 异常，这种方式也被称为异常包装或异常转译。

## raise 不需要参数

正如前面所看到的，在使用 raise 语句时可以不带参数，此时 raise 语句处于 except 块中，它将会自动引发当前上下文激活的异常；否则，通常默认引发 RuntimeError 异常。

例如，将上面程序改为如下形式：

```
class AuctionException(Exception): pass
class AuctionTest:
    def __init__(self, init_price):
        self.init_price = init_price
    def bid(self, bid_price):
        d = 0.0
        try:
            d = float(bid_price)
        except Exception as e:
            # 此处只是简单地打印异常信息
            print("转换出异常：", e)
            # 再次引发自定义异常
            raise
        if self.init_price > d:
            raise AuctionException("竞拍价比起拍价低，不允许竞拍！")
        initPrice = d
def main():
    at = AuctionTest(20.4)
    try:
        at.bid("df")
    except AuctionException as ae:
        # 再次捕获到 bid()方法中的异常，并对该异常进行处理
        print('main 函数捕捉的异常：', ae)
main()
```

正如从 13 行代码所看到的，此时程序在 except 块中只是简单地使用 raise 语句来引发异常，那么该 raise 语句将会再次引发该 except 块所捕获的异常。运行该程序，可以看到如下输出结果：

转换出异常： could not convert string to float: 'df'
main 函数捕获的异常：<class 'ValueError'>

从输出结果来看，此时 main() 函数再次捕获了 ValueError，它就是在 bid() 方法中 except 块所捕获的原始异常。