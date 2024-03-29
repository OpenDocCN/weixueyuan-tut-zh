# MATLAB 二维数组（矩阵）的创建

> 原文：[`c.biancheng.net/view/6697.html`](http://c.biancheng.net/view/6697.html)

MATLAB 二维数组与矩阵之间有很大的相关性，二维数组是由实数或复数排列成矩形构成的，而且从数据结构上看，矩阵和二维数组没有区别。

本节将讲解 MATLAB 二维数组的两种创建方式。

## 直接输入

矩阵的生成可以通过在方括号`[ ]`中输入元素实现，不同行之间要用分号`;`隔开，不同列用空格或者逗号`,`隔开。

例如，生成一个 2×3 的矩阵：

```

>> a=[1,2,3; 2,3,4]
a =
    1     2     3
    2     3     4
```

可以用冒号快速建立矩阵，步长在默认的情况下为 1，也可以自定义步长。具体程序如下：

```

>> A=[1:3; 4:6]
A =
    1     2     3
    4     5     6

>> B=[2:3:11; 4:2:10]
B =
    2     5     8    11
    4     6     8    10
```

## 使用矩阵生成函数

MATLAB 中提供了很多用于生成矩阵的函数，请看下表。

MATLAB 常用的矩阵生成函数

| 矩阵 | 函数 | 矩阵 | 函数 |
| 全零矩阵 | zeros() | 友矩阵 | compan() |
| 单位矩阵 | eye() | Hadamard 矩阵 | hadamard() |
| 全 1 矩阵 | ones() | Hankel 矩阵 | hankel() |
| 均匀分布随机矩阵 | rand() | Hilbert 矩阵 | hilb() |
| 正态分布随机矩阵 | randn() | 逆 Hilbert 矩阵 | invhilb() |
| 产生线性等分向量 | linspace() | Magic 矩阵 | magic() |
| 产生对数等分向量 | logspace() | Pascal 矩阵 | pascal() |
| Wilkinson 特征值测试矩阵 | wilkinson() | 拓普利兹矩阵 | toeplitz() |

请看下面的例子：

```

>> ones(2,4)    %产生（2x4）全 1 数组
ans =
     1     1     1     1
     1     1     1     1
>> randn('state',0)    %把正态随机数发生器置 0
>> randn(2,3)    %产生正态随机阵
ans =
   -0.4326    0.1253   -1.1465
   -1.6656    0.2877    1.1909
>> D=eye(3)    %产生 3x3 的单位阵
D =
     1     0     0
     0     1     0
     0     0     1
>> diag(D)    %取 D 阵的对角元
ans =
     1
     1
     1
>> diag(diag(D))    %外 diag 利用一维数组生成对角阵
ans =
     1     0     0
     0     1     0
     0     0     1
>> randsrc(3,20,[-3,-1,1,3],1)    %在 [-3,-1,1,3] 上产生 3x20 均布随机数组，随机发生器的状态设置为 1
ans =
  1 至 10 列
    -1    -1    -3     1    -3     1    -3     3     3    -3
     1    -3    -1    -1     3    -1    -3    -1     3    -3
    -3    -3    -1     1    -3     1     3     1    -3     3
  11 至 20 列
    -3     1     1     3    -1    -1    -1     1    -1    -3
    -1     1     3     3     3     3    -3    -3    -3     1
     3    -1    -3     1    -3    -1    -3    -1     1     1
```