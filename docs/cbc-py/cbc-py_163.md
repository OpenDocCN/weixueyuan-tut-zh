# Python linecache 用法：随机读取文件指定行

linecache 模块允许从 Python 源文件中随机读取指定行，并在内部使用缓存优化存储。由于该模块主要被设计成读取 Python 源文件，因此它会用 UTF-8 字符集来读取文本文件。

实际上，使用 linecache 模块也可以读取其他文件，只要该文件使用了 UTF-8 字符集存储。

linecache 模块包含以下常用函数：

*   linecache.getline(filename, lineno, module_globals=None)：读取指定模块中指定文件的指定行。其中 filename 指定文件名，lineno 指定行号。
*   linecache.clearcache()：清空缓存。
*   linecache.checkcache(filename=None)：检查缓存是否有效。如果没有指定 filename 参数，则默认检查所有缓存的数据。

下面程序示范了使用 linecache 模块来随机读取指定行：

```
import linecache
import random

# 读取 random 模块的源文件的第 3 行
print(linecache.getline(random.__file__, 3))
# 读取本程序的第 3 行
print(linecache.getline('linecache_test.py', 3))
# 读取普通文件的第 2 行
print(linecache.getline('utf_text.txt', 2))
```

上面程序示范了使用 linecache 模块随机读取指定模块源文件、Python 源程序、普通文件的指定行。运行程序后，即可看到使用 linecache 模块读取指定行的效果。