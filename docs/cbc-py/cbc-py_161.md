# Python for 循环读取文件

实际上，文件对象本身就是可遍历的（就像一个序列一样），因此，程序完全可以使用 for 循环来遍历文件内容。

例如，如下程序使用 for 循环读取文件内容：

```
import codecs
# 指定使用 utf-8 字符集读取文件内容
f = codecs.open("for_file.py", 'r', 'utf-8', buffering=True)
# 使用 for 循环遍历文件对象
for line in f:
    print(line, end='')
f.close()
```

如果有需要，程序也可以使用 list() 函数将文件转换成 list 列表，就像文件对象的 readlines() 方法的返回值一样。例如如下代码：

```
# 将文件对象转换为 list 列表
print(list(codecs.open("for_file.py", 'r', 'utf-8', buffering=True)))
```

此外，sys.stdin 也是一个类文件对象（类似于文件的对象，Python 的很多 I/O 流都是类文件对象），因此，程序同样可以使用 for 循环遍历 sys.stdin，这意味着程序可以通过 for 循环来获取用户的键盘输入。例如如下代码：

```
import sys
# 使用 for 循环遍历标准输入
for line in sys.stdin:
    print('用户输入:', line, end='')
```

上面代码使用 for 循环遍历 sys.stdin，这意味着程序可以通过 for 循环来读取用户的键盘输入，即用户每输入一行，程序就会输出用户输入的这行。