# Python 导入模块，Python import 用法（超级详细）

对于一个真实的 Python 程序，我们不可能自己完成所有的工作，通常都需要借助于第三方类库。此外，也不可能在一个源文件中编写整个程序的源代码，这些都需要以模块化的方式来组织项目的源代码。

前面章节中已经看到使用 import 导入模块的语法，但实际上 import 还有更多详细的用法，主要有以下两种：

1.  `import 模块名 1 [as 别名 1], 模块名 2 [as 别名 2]，…`： 导入整个模块。
2.  `from 模块名 import 成员名 1 [as 别名 1]，成员名 2 [as 别名 2]，…`： 导入模块中指定成员。

上面两种 import 语句的区别主要有以下几点：

*   第一种 import 语句导入整个模块内的所有成员（包括变量、函数、类等）；第二种 import 语句只导入模块内的指定成员（除非使用 form 模块名 import ＊，但通常不推荐使用这种语法）。
*   当使用第一种 import 语句导入模块中的成员时，必须添加模块名或模块别名前缀；使用第二种 import 语句导入模块中的成员时，无须使用任何前缀，直接使用成员名或成员别名即可。

下面程序使用导入整个模块的最简单语法来导入指定模块：

```
# 导入 sys 整个模块
import sys
# 使用 sys 模块名作为前缀来访问模块中的成员
print(sys.argv[0])
```

上面第 2 行代码使用最简单的方式导入了 sys 模块，因此在程序中使用 sys 模块内的成员时，必须添加模块名作为前缀。

运行上面程序，可以看到如下输出结果（sys 模块下的 argv 变量用于获取运行 Python 程序的命令行参数，其中 argv[0] 用于获取该 Python 程序的程序名）。

import test.py

在导入整个模块时可以为模块指定别名。例如如下程序：

```
# 导入 sys 整个模块，并指定别名为 s
import sys as s
# 使用 s 模块别名作为前缀来访问模块中的成员
print(s.argv[0])
```

第 2 行代码在导入 sys 模块时才指定了别名 s，因此在程序中使用 sys 模块内的成员时，必须添加模块别名 s 作为前缀。运行该程序，可以看到如下输出结果：

import test2.py

使用导入整个模块的语法也可一次导入多个模块，多个模块之间用逗号隔开。例如如下程序：

```
# 导入 sys、os 两个模块
import sys,os
# 使用模块名作为前缀来访问模块中的成员
print(sys.argv[0])
# os 模块的 sep 变量代表平台上的路径分隔符
print(os.sep)
```

上面第 2 行代码一次导入了 sys 和 os 两个模块，因此程序要使用 sys、os 两个模块内的成员，只要分别使用 sys、os 模块名作为前缀即可。在 Windows 平台上运行该程序，可以看到如下输出结果（os 模块的 sep 变量代表平台上的路径分隔符）：

import test3.py
\

同时，在导入多个模块时也可以为模块指定别名，例如如下程序：

```
# 导入 sys、os 两个模块，并为 sys 指定别名 s，为 os 指定别名 o
import sys as s,os as o
# 使用模块别名作为前缀来访问模块中的成员
print(s.argv[0])
print(o.sep)
```

上面第 2 行代码一次导入了 sys 和 os 两个模块，并分别为它们指定别名为 s、o，因此程序可以通过 s、o 两个前缀来使用 sys、os 两个模块内的成员。在 Windows 平台上运行该程序，可以看到如下输出结果：

import_test4 .py
\

接下来介绍使用 from...import 导入模块内指定成员的用法。下面程序使用 from...import 导入模块成员的最简单语法来导入指定成员：

```
# 导入 sys 模块的 argv 成员
from sys import argv
# 使用导入成员的语法，直接使用成员名访问
print(argv[0])
```

第 2 行代码导入了 sys 模块中的 argv 成员，这样即可在程序中直接使用 argv 成员，无须使用任何前缀。运行该程序，可以看到如下输出结果：

from import test.py

使用 from...import 导入模块成员时也可为成员指定别名，例如如下程序：

```
# 导入 sys 模块的 argv 成员，并为其指定别名 v
from sys import argv as v
# 使用导入成员（并指定别名）的语法，直接使用成员的别名访问
print(v[0])
```

第 2 行代码导入了 sys 模块中的 argv 成员，并为该成员指定别名 v，这样即可在程序中通过别名 v 使用 argv 成员，无须使用任何前缀。运行该程序，可以看到如下输出结果：

from import test2.py

在使用 form...import 导入模块成员时也可同时导入多个成员，例如如下程序：

```
# 导入 sys 模块的 argv,winver 成员
from sys import argv, winver
# 使用导入成员的语法，直接使用成员名访问
print(argv[0])
print(winver)
```

上面第 2 行代码导入了 sys 模块中的 argv、 winver 成员，这样即可在程序中直接使用 argv、winver 两个成员，无须使用任何前缀。运行该程序，可以看到如下输出结果（sys 的 winver 成员记录了该 Python 的版本号）：

from import test3.py
3.6

在使用 from...import 同时导入多个模块成员时也可指定别名，同样使用 as 关键字为成员指定别名，例如如下程序：

```
# 导入 sys 模块的 argv,winver 成员，并为其指定别名 v、wv
from sys import argv as v, winver as wv
# 使用导入成员（并指定别名）的语法，直接使用成员的别名访问
print(v[0])
print(wv)
```

上面第 2 行代码导入了 sys 模块中的 argv、winver 成员，并分别为它们指定了别名 v、wv，这样即可在程序中通过 v 和 wv 两个别名使用 argv、winver 成员，无须使用任何前缀。运行该程序，可以看到如下输出结果：

在使用 from...import 语法时也可一次导入指定模块内的所有成员，例如如下程序：

```
#导入 sys 棋块内的所有成员
from sys import *
#使用导入成员的语法，直接使用成员的别名访问
print(argv[0])
print(winver)
```

上面代码一次导入了 sys 模块中的所有成员，这样程序即可通过成员名来使用该模块内的所有成员。该程序的输出结果和前面程序的输出结果完全相同。

需要说明的是，一般不推荐使用“from 模块 import”这种语法导入指定模块内的所有成员，因为它存在潜在的风险。比如同时导入 module1 和 module2 内的所有成员，假如这两个模块内都有一个 foo() 函数，那么当在程序中执行如下代码时：

foo()

上面调用的这个 foo() 函数到底是 module1 模块中的还是 module2 模块中的？因此，这种导入指定模块内所有成员的用法是有风险的。

但如果换成如下两种导入方式：

import module1
import module2 as m2

接下来要分别调用这两个模块中的 foo() 函数就非常清晰。程序可使用如下代码：

```
#使用模块 module1 的模块名作为前缀调用 foo()函数
module1.foo()
#使用 module2 的模块别名作为前缀调用 foo()函数
m2.foo()
```

或者使用 from...import 语句也是可以的：

```
#导入 module1 中的 foo 成员，并指定其别名为 foo1
from module1 import foo as fool
#导入 module2 中的 foo 成员，并指定其别名为 foo2
from module2 import foo as foo2
```

此时通过别名将 module1 和 module2 两个模块中的 foo 函数很好地进行了区分，接下来分别调用两个模块中 foo() 函数就很清晰：

foo1() #调用 module1 中的 foo()函数
foo2() #调用 module2 中的 foo()函数