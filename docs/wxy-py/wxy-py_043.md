# Python 函数参数的传递（精讲版）

> 原文：[`www.weixueyuan.net/a/549.html`](http://www.weixueyuan.net/a/549.html)

多数函数是带有参数的，通过给定不同的输入参数进行不同的操作，并得到不同的返回值。Python 的参数就是函数和调用者之间的通信协议。作为通信协议，最重要的是保持一致，即调用者和函数之间对参数的理解要求一致。

## 位置参数

最简单的协议是类似于 C 语言的形参和实参。函数定义了形参，调用者传入实参。要求形参和实参个数相同，顺序也相同，即形参和实参是按照位置来匹配的。

例如，求两个参数的和，可以按以下方式定义：

```

def get_sum(a, b):  # 定义了两个形参，一个是 a，一个是 b
    return a+b
get_sum(1, 2)       # 传递进去两个实参，一个是 1，一个是 2
```

如果参数个数不同，会导致异常。下面的例子演示了形参和实参不匹配的情形：

```

>>> def get_sum(a, b):  # 定义函数
        return a+b

>>> get_sum(1)  # 实参个数比形参少
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: get_sum() takes exactly 2 arguments (1 given)
>>> get_sum(1, 2, 3)  # 实参个数比形参多
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: get_sum() takes exactly 2 arguments (3 given)
```

如果参数的顺序不一致，可能不会抛出异常，但计算结果不是我们预期的。

例如，要求两个数的差，在函数定义时第一个参数表示被减数，第二个参数表示减数。但如果在调用时将顺序颠倒，第一个实参是减数，第二个实参是被减数，得到的结果就是错误的。

```

>>> def get_diff(a, b):  # 定义求差的函数，第一个参数为被减数
        return a-b

>>> get_diff(10, 2)      # 使用求差函数，但是实参顺序不对，导致结果错误
8
```

## 将位置参数当作元组

上面的办法有一个缺点，即函数的参数个数是确定的，但有些情况不能确定调用函数时输入的实参数量。最简单的例子就是 Python 3 中的 print() 函数，其可以接受任意多个参数。

```

>>> print(1)        # 1 个参数
1
>>> print(1, 2)     # 2 个参数
1 2
>>> print(1, 2, 3)  # 3 个参数
1 2 3
```

假定现在需要实现一个求和函数，但并不局限在两个数或者三个数的求和，即该函数要求能够接受任意数量的参数，然后求出所有实参的和并返回该和。

这时可以将所有的实参当作一个元组，定义函数如下：

def get_sum(*l):

这里和前面不同的是参数前面带有一个`*`，表示参数`l`是一个元组，该元组的每个元素都对应一个实参，而且顺序和实参的实际顺序一致。

```

>>> def get_sum(*l):  # 定义求和函数，将参数当作元组
        print("Type of l:", type(l))
        ret = 0
        for x in l:
            print("  x =", x)
            ret = ret + x
        print("sum =", ret)
        return ret
                      # 求和函数定义结束
>>> get_sum(10, 2)  # 使用求和函数，传入两个实参
Type of l: <class 'tuple'>
  x = 10
  x = 2
sum = 12
12
>>> get_sum(10, 2, 11, 28, 100)  # 使用求和函数，传入 5 个实参
Type of l: <class 'tuple'>
  x = 10
  x = 2
  x = 11
  x = 28
  x = 100
sum = 151
151
```

前面的例子是将所有的实参作为一个元组使用。那能否将部分实参作为元组使用呢？答案是可以的，但作为元组使用的参数一定要是最后的实参。我们可以将前面的实参当作普通参数使用，但后面的参数当作一个元组使用。

下面修改一下前面的求和函数，其第一个参数是一个系数 k，后面是任意多个参数，其计算的结果是这些参数的和，然后乘以第一个参数 k。用数学来表达就是：

y = k * (v1 + v2+ ...)

这时我们可将第一个实参 k 作为普通的参数，其他的实参作为元组。

下面是该函数的定义和使用情况：

```

>>> def get_sum(k, *l):  # 第一个实参是普通参数，后面的是一个元组
        print("k =", k)
        print("Type of l:", type(l))
        ret = 0
        for x in l:  # 对元组内的数据求和
            print("  x =", x)
            ret = ret + x
        ret = ret * k  # 乘以系数 k
        print("sum =", ret)
        return ret

>>> get_sum(10, 2)  # 10 对应的是 k，2 放入到元组参数中
k = 10
Type of l: <class 'tuple'>
  x = 2
sum = 20
20
>>> get_sum(10, 2, 11, 28, 100)  # 10 对应的是 k，其他放入到元组参数中
k = 10
Type of l: <class 'tuple'>
  x = 2
  x = 11
  x = 28
  x = 100
sum = 1410
1410
```

## 调用时使用元组和列表

上面在调用函数时采用的是普通的方法，但是在函数定义时对参数进行了特殊处理。这里正好相反，即在函数定义时使用普通的方法，但在函数调用时传入的是元组。

注意，该元组中元素的个数和顺序一定要和函数定义时一致；传入的字典对象前需要加上一个`*`。

```

>>> def func_demo(a, b, c):  # 普通定义方法，需要 3 个参数
        print("  a = %s" % a)
        print("  b = %s" % b)
        print("  c = %s" % c)

>>> d = [1, 2, 3]  # 传入的是列表
>>> func_demo(*d)  # 列表有 3 个元素，没有问题
  a = 1            # 注意参数对象前面的*
  b = 2
  c = 3
>>> d = [1, 2]     # 列表只有 2 个元素，有问题
>>> func_demo(*d)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: func_demo() missing 1 required positional argument: 'c'
>>> d = (10, 20, 30)  # 参数是元组，元素个数为 3，也没有问题
>>> func_demo(*d)
  a = 10
  b = 20
  c = 30
```

## 关键字参数

将实参当作元组能解决参数个数不确定的问题，但还有一个问题，即参数顺序必须一致。如果不希望调用时固定参数的顺序，可以在调用时同时指定形参和实参，这些参数又称为关键字参数。

```

>>> def get_diff(sub_from, sub_val):
        ret = sub_from - sub_val
        print("  %d - %d = %d" % (sub_from, sub_val, ret))
        return ret

>>> get_diff(100, 10)  # 普通调用方法，要求个数和顺序正确
  100 - 10 = 90
90
>>> get_diff(sub_from=100, sub_val=10) # 不要求顺序一致
  100 - 10 = 90
90
>>> get_diff(sub_val=10, sub_from=100) # 不要求顺序一致
  100 - 10 = 90
90
```

## 将实参当作字典

对于同时使用形参和实参，同样也会面临参数个数的问题。如果不确定参数个数，该如何处理呢？最简单的一个例子就是命令行参数，有些参数被指定了，有些没有被指定，从而导致实参的个数不定。

为了解决该问题，可以将实参当作字典，该字典的键就是形参的名字，值就是实参。由于是字典，所以其元素个数是不定的，即有些参数可能存在也可能不存在。

假定某个函数，其最多可以有 3 个参数 key1、key2 和 key3，在调用时可以指定某一个参数，也可以指定某两个参数或者都指定，则该函数可以进行如下定义：

```

>>> def func_demo(**d):  # 这里 d 匹配所有带有形参和实参的参数
        print("type of d: %s" % type(d))
        for k, v in d.items():
            print("  %s => %s" % (k, v))
        return None

>>> func_demo(key1=1)  # 只传入 1 个参数 key1
type of d: <class 'dict'>
  key1 => 1
>>> func_demo(key2=1, key1=2)  # 只传入 2 个参数，key2 和 key1
type of d: <class 'dict'>
  key2 => 1
  key1 => 2
>>> func_demo(key2=1, key3=6, key1=2)  # 传入 3 个参数，key2、key3 和 key1
type of d: <class 'dict'>
  key2 => 1
  key3 => 6
  key1 => 2
```

可以看到，使用字典来表示参数时，既不要求参数个数固定，也不要求参数顺序固定，这比前面的用法要灵活得多。

## 调用时使用字典

和前面类似，可以在函数定义时使用普通用法，但在调用时通过传入一个字典来解决。这时需要在传入参数前面加上两个`*`。同样，实参个数一定要等于形参个数。由于传入的是字典，所以顺序是没有关系的。

```

>>> def func_demo(a, b, c):  # 普通函数定义方法
        print("  a = %s" % a)
        print("  b = %s" % b)
        print("  c = %s" % c)

>>> d = {"a": 10, "b":20, "c":30}  # 传入字典
>>> func_demo(**d)  # 注意字典对象前的**
  a = 10
  b = 20
  c = 30
>>> d = {"c":30, "a": 10, "b":20}  # 参数顺序不重要
>>> func_demo(**d)
  a = 10
  b = 20
  c = 30
>>> d = {"a": 10, "b":20}  # 但是参数的个数必须和形参一致
>>> func_demo(**d)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: func_demo() missing 1 required positional argument: 'c'
```

## 默认值

在定义函数时可以指定参数的默认值。这样在调用时即使不指定该参数的值也没有关系，因为该参数会自动等于定义时指定的默认值。

需要注意的是，带有默认值的参数一定是在最后面，不能放在没有默认值参数的前面。

```

>>> def get_sum(a, b=0):  # 参数 b 有一个默认值 0，如果未指定 b 的值，b 就为 0
        return a+b

>>> get_sum(1, 2)         # 普通用法，指定了 a 和 b 的值
3
>>> get_sum(1)            # 没有指定 b 的值，所以 b 等于默认值 0
1
```

如果有默认值的参数在没有默认值参数的前面，那么在函数定义时会抛出语法错误的异常。下面演示了这种情况。

```

>>> def get_sum(a=0, b):  # a 有默认值，b 没有默认值，a 不能在 b 的前面
        return a+b

  File "<stdin>", line 1
SyntaxError: non-default argument follows default argument
```