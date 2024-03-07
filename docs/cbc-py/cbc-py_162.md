# Python with 用法：自动关闭文件

在前面的程序中，我们都采用了程序主动关闭文件的方式。实际上，Python 提供了 with 语句来管理资源关闭。比如可以把打开的文件放在 with 语句中，这样 with 语句就会帮我们自动关闭文件。

with 语句的语法格式如下：

with context expression [as target(s)]:
    with 代码块

在上面的语法格式中，context_expression 用于创建可自动关闭的资源。

例如，程序使用 with 语句来读取文件：

```
import codecs
# 使用 with 语句打开文件，该语句会负责关闭文件
with codecs.open("readlines_test.py", 'r', 'utf-8', buffering=True) as f:
    for line in f:
        print(line, end='')
```

程序也可以使用 with 语句来处理通过 fileinput.input 合并的多个文件，例如如下程序：

```
import fileinput
# 使用 with 语句打开文件，该语句会负责关闭文件
with fileinput.input(files=('test.txt', 'info.txt')) as f:
    for line in f:
        print(line, end='')
```

上面两个程序都使用了 with 语句来管理资源，因此它们都不需要显式关闭文件。

那么，with 语句的实现原理是什么？其实很简单，使用 with 语句管理的资源必须是一个实现上下文管理协议（context manage protocol）的类，这个类的对象可被称为上下文管理器。要实现上下文管理协议，必须实现如下两个方法：

1.  context_manager.__enter__()：进入上下文管理器自动调用的方法。该方法会在 with 代码块执行之前执行。如果 with 语句有 as 子句，那么该方法的返回值会被赋值给 as 子句后的变量；该方法可以返回多个值，因此，在 as 子句后面也可以指定多个变量（多个变量必须由“()”括起来组成元组）。
2.  context_manager.__exit__（exc_type, exc_value, exc_traceback）：退出上下文管理器自动调用的方法。该方法会在 with 代码块执行之后执行。如果 with 代码块成功执行结束，程序自动调用该方法，调用该方法的三个参数都为 None：如果 with 代码块因为异常而中止，程序也自动调用该方法，使用 sys.exc_info 得到的异常信息将作为调用该方法的参数。

通过上面的介绍不难发现，只要一个类实现了 __enter__() 和 __exit__(exc_type, exc_value, exc_traceback) 方法，程序就可以使用 with 语句来管理它；通过 __exit__() 方法的参数，即可判断出 with 代码块执行时是否遇到了异常。

换而言之，上面程序所用的文件对象、FileInput 对象，其实都实现了这两个方法，因此它们都可以接受 with 语句的管理。

下面我们自定义一个实现上下文管理协议的类，并使用 with 语句来管理它：

```
class FkResource:
    def __init__(self, tag):
        self.tag = tag
        print('构造器,初始化资源: %s' % tag)
    # 定义 __enter__ 方法，with 体之前的执行的方法
    def __enter__(self):
        print('[__enter__ %s]: ' % self.tag)
        # 该返回值将作为 as 子句中变量的值
        return 'fkit'  # 可以返回任意类型的值
    # 定义 __exit__ 方法，with 体之后的执行的方法
    def __exit__(self, exc_type, exc_value, exc_traceback):
        print('[__exit__ %s]: ' % self.tag)
        # exc_traceback 为 None，代表没有异常
        if exc_traceback is None:
            print('没有异常时关闭资源')
        else:
            print('遇到异常时关闭资源')
            return False   # 可以省略，默认返回 None 也被看做是 False

with FkResource('孙悟空') as dr:
    print(dr)
    print('[with 代码块] 没有异常')
print('------------------------------')
with FkResource('白骨精'):
    print('[with 代码块] 异常之前的代码')
    raise Exception
    print('[with 代码块] ~~~~~~~~异常之后的代码')
```

上面程序定义了一个 FkResource 类，该类定义了 __enter__() 和 __exit__() 两个方法，因此该类的对象可以被 with 语句管理：

*   程序在执行 with 代码块之前，会执行 __enter__() 方法，并将该方法的返回值赋值给 as 子句后的变量。
*   程序在执行 with 代码块之后，会执行 __exit__() 方法，可以根据该方法的参数来判断 with 代码块是否有异常。

程序两次使用 with 语句管理 FkResource 对象。第一次，with 代码块没有出现异常。第二次，with 代码块出现了异常。大家可以看到，使用 with 语句两次对 FkResource 的管理略有差异（主要是在 __exit()__ 方法中略有差异）。

运行上面的程序，可以看到如下输出结果：

构造器,初始化资源: 孙悟空
[__enter__ 孙悟空]:
fkit
[with 代码块] 没有异常
[__exit__ 孙悟空]:
没有异常时关闭资源
------------------------------
构造器,初始化资源: 白骨精
[__enter__ 白骨精]:
[with 代码块] 异常之前的代码
[__exit__ 白骨精]:
遇到异常时关闭资源
Traceback (most recent call last):
  File "C:\Users\mengma\Desktop\1.py", line 26, in <module>
    raise Exception
Exception

从上面的输出结果来看，使用 with 语句管理资源，程序总可以在进入 with 代码块之前自动执行 __enter__() 方法，无论 with 代码块是否有异常，这个部分都是一样的，而且 __enter__() 方法的返回值被赋值给了 as 子句后的变量，如上面的 ① 号输出信息所示。

对于 with 代码块有异常和无异常这两种情况，此时主要通过 exit() 方法的参数进行判断，程序可针对 with 代码块是否有异常分别进行处理，如程序中代码所示。