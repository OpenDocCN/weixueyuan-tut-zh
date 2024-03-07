# Python ChainMap 用法

ChainMap 是一个方便的工具类，它使用链的方式将多个 dict“链”在一起，从而允许程序可直接获取任意一个 dict 所包含的 key 对应的 value。

简单来说，ChainMap 相当于把多个 dict 合并成一个大的 dict，但实际上底层并未真正合并这些 dict，因此程序无须调用多个 update() 方法将多个 dict 进行合并。所以说 ChainMap 是一种合并，但实际用起来又具有较好的效果。

需要说明的是，由于 ChainMap 只是将多个 dict 链在一起，并未真正合并它们，因此在多个 dict 中完全可能具有重复的 key，在这种情况下，排在“链”前面的 dict 中的 key 具有更高的优先级。

例如，下面程序示范了 ChainMap 的用法：

```
from collections import ChainMap
# 定义 3 个 dict 对象
a = {'Kotlin': 90, 'Python': 86}
b = {'Go': 93, 'Python': 92}
c = {'Swift': 89, 'Go': 87}
# 将 3 个 dict 对象链在一起，就像变成了一个大的 dict
cm = ChainMap(a, b , c)
print(cm)
# 获取 Kotlin 对应的 value
print(cm['Kotlin']) # 90
# 获取 Python 对应的 value
print(cm['Python']) # 86
# 获取 Go 对应的 value
print(cm['Go']) # 93
```

上面程序 a、b、c 三个 dict 链在一起，组成一个 ChainMap，这个链的顺序就是 a、b、c，因此 a 的优先级最高，b 的优先级次之，c 的优先级最低。运行上面程序，可以看到如下输出结果：

ChainMap({'Kotlin': 90, 'Python': 86}, {'Go': 93, 'Python': 92}, {'Swift': 89, 'Go': 87})
90
86
93

从上面第一行输出可以看到，ChainMap 其实并未将这三个 dict 合并成一个大的 dict，只是将它们链在一起而己。

掌握了 ChainMap 的用法之后，接下来介绍的示例程序借鉴了 Python 库文档中的一个例子，该例子将局部范围的定义、全局范围的定义、Python 内置定义链成一个 ChainMap，当程序通过该 ChainMap 获取变量时，将会按照局部定义、全局定义、内置定义的顺序执行搜索。

```
from collections import ChainMap
import builtins
my_name = '孙悟空'
def test():
    my_name = 'yeeku'
    # 将 locals、globals、buliltins 的变量链接成 ChainMap
    pylookup = ChainMap(locals(), globals(), vars(builtins))
    # 访问 my_name 对应的 value，优先使用局部范围的定义
    print(pylookup['my_name']) # 'yeeku'
    # 访问 len 对应的 value，由于局部范围、全区范围都找不到，因此访问内置定义的 len 函数
    print(pylookup['len']) # <built-in function len>
test()
```

下面程序示范了优先使用运行程序的指定参数，然后是系统环境变量，最后才使用系统默认值的实现，程序同样将这三个搜索范围链成 ChainMap：

```
#程序文件名为 ChainMap_test.py
from collections import ChainMap
import os, argparse
# 定义默认参数
defaults = {'color': '蓝色', 'user': 'yeeku'}
# 创建程序参数解析器
parser = argparse.ArgumentParser()
# 为参数解析器添加-u（--user）和-c（--color）参数
parser.add_argument('-u', '--user')
parser.add_argument('-c', '--color')
# 解析运行程序的参数
namespace = parser.parse_args()
# 将程序参数转换成 dict
command_line_args = {k:v for k, v in vars(namespace).items() if v}
# 将 command_line_args(解析程序参数而来)、os.environ(环境变量)、defaults 链成 ChainMap
combined = ChainMap(command_line_args, os.environ, defaults)
# 获取 color 对应的 value
print(combined['color'])
# 获取 user 对应的 value
print(combined['user'])
# 获取 PYTHONPATH 对应的 value
print(combined['PYTHONPATH'])
```

上面程序将 command_line_args、as.environ、defaults 链成 ChairMap，其中 command_line_args 由程序参数解析而来，它的优先级最高。

假如使用如下命令来运行该程序：

python ChainMap_test.py -c 红色 -u Charlie

由于上面命令指定了 -c（对应于 color）和 -u（对应于 user）参数，在命令行指定的参数的优先级是最高的，因此可以看到如下输出结果：

红色
Charlie
.;d:\python_module

上面输出的最后一行是 PYTHONPATH 环境变量的值。如果使用如下命令来运行该程序：

python ChainMap_test.py

由于上面命令没有指定任何命令行参数，因此程序访问 user、color 时将会使用 defaults 字典中 key 对应的值，所以可以看到如下输出结果：

蓝色
yeeku
.;d:\python_ module