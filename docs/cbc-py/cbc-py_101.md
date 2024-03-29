# Python 自定义模块

掌握了导入模块的语法之后，下一个问题来了，模块到底是什么？可能有读者开始摩拳擦掌了，怎样才能定义自己的模块呢？

模块就是 Python 程序。任何 Python 程序都可作为模块导入。前面我们写的所有 Python 程序都可作为模块导入。换而言之，随便写的一个 Python 程序，其实都可作为模块导入。对于任何程序，只要导入了模块，即可使用该模块内的所有成员。

下面程序定义了一个简单的模块（编写在 module1.py 文件中）：

```
print('这是 module 1')
my_book = 'Python 入门教程'
def say_hi(user):
    print('%s,您好，欢迎学习 Python' % user)
class User:
    def __init__(self, name):
        self.name = name
    def walk(self):
        print('%s 正在慢慢地走路' % self.name)
    def __repr__(self):
        return 'User[name=%s]' % self.name
```

上面程序中第一行代码执行了一条简单的输出语句，第二行代码定义了一个 my_book 变量。接下来程序定义了一个 say_hi() 函数，然后定义了一个 User 类。不难发现，这个程序和我们前面所写的 Python 程序并没有太大的区别，但它依然可以作为模块导入。

使用模块的好处在于，如果将程序需要使用的程序单元（比如刚刚定义的 say_hi() 函数、User 类）定义在模块中，后面不管哪个程序，只要导入该模块，该程序即可使用该模块所包含的程序单元，这样就可以提供很好的复用（导入模块，使用模块），从而避免每个程序都需要重新定义这些程序单元。

模块文件的文件名就是它的模块名，比如 module1.py 的模块名就是 module1。

## 为模块编写说明文档

与前面介绍的函数、类相同的是，在实际开发中往往也应该为模块编写说明文档；否则，其他开发者将不知道该模块有什么作用，以及包含哪些功能。

为模块编写说明文档很简单，只要在模块开始处定义一个字符串直接量即可。例如，在上面程序的第一行代码之前添加如下内容：

'''
这是我们编写的第一个模块，该模块包含以下内容：
my_book：字符串变量
say_hi：简单的函数
User：代表用户的类
'''

这段字符串内容将会作为该模块的说明文挡，可通过模块的 __doc__ 属性来访问文档。

## 为模块编写测试代码

当模块编写完成之后，可能还需要为模块编写一些测试代码，用于测试模块中的每一个程序单元是否都能正常运行。

由于模块其实也是一个 Python 程序，因此我们完全可以使用 python 命令来解释和执行模块程序只要模块中包含可执行代码。比如使用 python module1.py（程序文件名）命令来运行上面的模块程序，即可看到如下输出结果：

这是 module 1

上面这行输出是因为 module1.py 程序中包含了一条 print 语句。但棋块中的变量、函数、类都没有得到测试，因此还应该为这些变量、函数、类提供测试程序。

例如，可以为上面的 module1.py 增加如下测试代码：

```
# ===以下部分是测试代码===
def test_my_book ():
    print(my_book)
def test_say_hi():
    say_hi('孙悟空')
    say_hi(User('Charlie'))
def test_User():
    u = User('白骨精')
    u.walk()
    print(u)
```

上面代码为 module1 定义了三个函数，分别用于测试模块中的变量、函数和类，不过这三个函数并没有得到调用的机会。因此，如果使用 python 命令来运行上面模块，程序并不会运行它们。

注意：对于实际开发的项目，对每个函数、类可能都需要使用更多的测试用例进行测试，这样才能达到各种覆盖效果（比如语句覆盖、条件覆盖等）。本教程并不介绍测试相关内容，因此就不深入展开了。

如果只是简单地调用上面的测试程序，则会导致一个问题，那就是当其他程序每次导入该模块时，这三个测试函数都会自动运行，这显然不是我们期望看到的结果。此时希望实现的效果是，如果直接使用 python 命令运行该模块（相当于测试），程序应该执行该模块的测试函数；如果是其他程序导入该模块，程序不应该执行该模块的测试函数。

此时可借助于所有模块内置的 __name__ 变量进行区分，如果直接使用 python 命令来运行一个模块，name 变量的值为 __main__；如果该模块被导入其他程序中，__name__ 变量的值就是模块名。因此，如果希望测试函数只有在使用 python 命令直接运行时才执行，则可在调用测试函数时增加判断，只有当 __name__ 属性为 __main__ 时才调用测试函数。为模块增加如下代码即可：

```
# 当 __name__ 为'__main__'（直接使用 python 运行该模块）时执行如下代码
if __name__ == '__main__':
    test_my_book()
    test_say_hi()
    test_User()
```

此时再次使用 python module1.py 命令来运行该模块，可以看到如下输出结果：

这是 module 1
Python 入门教程
孙悟空,您好，欢迎学习 Python
User[name=Charlie],您好，欢迎学习 Python
白骨精正在慢慢地走路
User[name=白骨精]

从上面的测试结果来看，当直接使用 python 命令来运行模块时，模块中的测试函数得到了执行。在定义好模块之后，接下来就要让 Python 系统能找到并加载该模块。