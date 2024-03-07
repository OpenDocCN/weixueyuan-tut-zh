# Python os.path 模块常见函数用法（实例+详细注释）

os.path 模块下提供了一些操作目录的方法，这些函数可以操作系统的目录本身。该模块提供了 exists() 函数判断该目录是否存在；也提供了 getctime()、getmtime()、getatime() 函数来获取该目录的创建时间、最后一次修改时间、最后一次访问时间；还提供了 getsize() 函数来获取指定文件的大小。

下面程序示范了 os.path 模块下的操作目录的常见函数的功能和用法：

```
import os
import time

# 获取绝对路径
print(os.path.abspath("abc.txt")) # G:\publish\codes\12\12.2\abc.txt
# 获取共同前缀
print(os.path.commonprefix(['/usr/lib', '/usr/local/lib'])) # /usr/l
# 获取共同路径
print(os.path.commonpath(['/usr/lib', '/usr/local/lib'])) # \usr
# 获取目录
print(os.path.dirname('abc/xyz/README.txt')) #abc/xyz
# 判断指定目录是否存在
print(os.path.exists('abc/xyz/README.txt')) # False
# 获取最近一次访问时间
print(time.ctime(os.path.getatime('os.path_test.py')))
# 获取最后一次修改时间
print(time.ctime(os.path.getmtime('os.path_test.py')))
# 获取创建时间
print(time.ctime(os.path.getctime('os.path_test.py')))
# 获取文件大小
print(os.path.getsize('os.path_test.py'))
# 判断是否为文件
print(os.path.isfile('os.path_test.py')) # True
# 判断是否为目录
print(os.path.isdir('os.path_test.py')) # False
# 判断是否为同一个文件
print(os.path.samefile('os.path_test.py', './os.path_test.py')) # True
```

运行上面程序，大部分函数的输出结果都通过注释给出了，程序中 getatime()、getmtime()、getctime() 三个函数分别获取了文件的最后一次访问时间、最后一次修改时间和创建时间。读者可通过运行该程序来理解 os.path 模块下这些函数的功能。