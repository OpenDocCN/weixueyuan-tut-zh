# MATLAB 一维数组（向量）的定义

> 原文：[`c.biancheng.net/view/6695.html`](http://c.biancheng.net/view/6695.html)

在 MATLAB 中，向量和矩阵主要是由数组表示的。

数组运算始终是 MATLAB 的核心内容，并且 MATLAB 区别于其他编程语言最大的优势就是数组计算。这种编程的优势使得计算程序简单、易读，程序命令更接近教科书上的数学公式，而且提高程序的向量化程度和计算效率，节省计算机的开销。

#### 矩阵和向量的概念

矩阵其实就是由 m×n 个元素组成的一个 m 行 n 列的二维数组。特别地，一个 m×1（m 行 1 列）矩阵也称为一个 m 维列向量，而一个 1×n（1 行 n 列）矩阵也称为一个 n 维行向量。

依照上面的定义可以看出：向量就是只有一行或者一列的特殊矩阵，而矩阵包含向量。

在 MATLAB 中，一维数组就是向量，二维数组就是矩阵。

## 向量（一维数组）的定义

在 MATLAB 中，我们可以直接输入向量，也可以使用冒号或者函数生成向量。

#### 1) 直接输入向量

直接输入行向量：

```

>> a=[1,3,2,4]
a=
    1  3  2  4
```

直接输入列向量：

```

>> a=[1;3;2;4]
a=
    1
    3
    2
    4
```

输入行向量时，各个元素之间以逗号`,`分隔；输入列向量时，各个元素之间以分号`;`分隔。行向量与列向量之间的区别仅仅是元素之间的分隔符而已，所以在使用直接输入创建一唯数组或向量时应注意元素之间的分隔符。

直接输入是针对小型的一维数组。

#### 2) 用`:`生成等差向量

生成等差向量可以使用下面的格式：

a=j:k

其中 j 表示起始数字，k 表示终止数字，数字之间的差值是 1，或者说步长是 1。这种格式最终生成的向量是

a=[j, j+1, ..., k-1, k]

请看下面的例子：

```

>> a=2:6
a=
    2  3  4  5  6
```

如果你想控制步长，或者控制数字之间的差值，可以使用下面的格式：

a=j:d:k

其中 j 表示起始数字，d 表示步长（数字之间的差值），k 表示终止数字。这种格式最终生成的向量是：

a=[j, j+d,..., j+m*d]

其中 m=fix((k-j)/d)，fix() 函数用来向下取整。

请看下面的例子：

>> a=2:3:20
a =
    2     5     8    11    14    17    20

>> a=2:3:22
a =
    2     5     8    11    14    17    20

这里建立的向量都是属于等差向量，在编程时可以使用这种快捷的方式建立一个等差向量。

#### 3) linspace() 函数

linspace() 函数用来生成按等差形式排列的行向量，它的第一种用法为：

x=linspace(X1, X2)

linspace() 会在 X1 和 X2 之间默认生成 100 个线性分布的数据，相邻两个数据的差保持不变，构成等差数列。

下面的例子会自动生成在 1～2 之间 100 个线性分布的向量，向量第一个数为 1，向量的最后一个数为 2。

```

>> a=linspace(1,2)
```

由于结果太多，在此就不一一列出了，读者可以自己运行一下。

如果你想控制生成的元素的数目，可以使用下面的格式：

x=linspace(X1, X2, n)

在 X1 和 X2 间生成 n 个线性分布的数据，相邻两个数据的差保持不变，同样能构成等差数列。

```

>> a=linspace(1,2,15)
a =
    1 至 6 列
        1.0000    1.0714    1.1429    1.2143    1.2857    1.3571
    7 至 12 列
        1.4286    1.5000    1.5714    1.6429    1.7143    1.7857
    13 至 15 列
        1.8571    1.9286    2.0000
```

用 linspace() 得出来的是一个线性分布的等差数列数组，在编程时有时需要得到按等比形式排列的一维数组，这时可以使用 logspace()，读者请自己学习。