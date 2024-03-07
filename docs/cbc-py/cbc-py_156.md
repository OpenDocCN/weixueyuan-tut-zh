# Python fnmatch 模块：用于文件名的匹配

前面介绍的那些操作目录的函数只能进行简单的模式匹配，但 fnmatch 模块可以支持类似于 UNIX shell 风格的文件名匹配。

fnmatch 匹配支持如下通配符：

*   *：可匹配任意个任意字符。
*   ？：可匹配一个任意字符。
*   [字符序列]：可匹配中括号里字符序列中的任意字符。该字符序列也支持中画线表示法。比如[a-c]可代表 a、b 和 c 字符中任意一个。
*   [!字符序列]：可匹配不在中括号里字符序列中的任意字符。

在该模块下提供了如下函数：

*   fnmatch.fnmatch(filename, pattern)：判断指定文件名是否匹配指定 pattern。如下程序示范了 fnmatch() 函数的用法：

    ```
    from pathlib import *
    import fnmatch

    # 遍历当前目录下所有文件和子目录
    for file in Path('.').iterdir():
        # 访问所有以 _test.py 结尾的文件
        if fnmatch.fnmatch(file, '*_test.PY'):
            print(file)
    ```

    上面程序先遍历当前目录下的所有文件和子目录，然后第 7 行代码调用 fnmatch() 函数判断所有以 _test.py 结尾的文件，并将该文件打印出来。
*   fnmatch.fnmatchcase(filename, pattern)：该函数与上一个函数的功能大致相同，只是该函数区分大小写。
*   fnmatch.filter(names, pattern)：该函数对 names 列表进行过滤，返回 names 列表中匹配 pattern 的文件名组成的子集合。如下代码示范了该函数的功能：

    ```
    names = ['a.py', 'b.py', 'c.py', 'd.py']
    # 对 names 列表进行过滤
    sub = fnmatch.filter(names, '[ac].py')
    print(sub) # ['a.py', 'c.py']
    ```

    上面程序定义了一个 `['a.py', 'b.py', 'c.py', 'd.py']` 集合，该集合中的 4 个元素都代表了指定文件（实际上文件是否存在，fnmatch 模块并不关心）。接下来程序中第 3 行代码调用了 filter() 函数对 names 进行过滤，过滤完成后只保留匹配 [ac].py 模式的文件名（要求文件名要么是 a.py，要么是 c.py）。
*   fnmatch.translate(pattern)：该函数用于将一个 UNIX shell 风格的 pattern 转换为正则表达式 pattern。如下代码示范了该函数的功能：

    ```
    print(fnmatch.translate('?.py')) # (?s:.\.py)\Z
    print(fnmatch.translate('[ac].py')) # (?s:[ac]\.py)\Z
    print(fnmatch.translate('[a-c].py')) # (?s:[a-c]\.py)\Z
    ```