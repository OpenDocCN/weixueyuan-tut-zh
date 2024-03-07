# MATLAB NaN 详解

> 原文：[`c.biancheng.net/view/6779.html`](http://c.biancheng.net/view/6779.html)

NaN 是 Not a Number 的缩写，意思是“不是一个有效的数字”。

MATLAB NaN 表示一个没有意义的数字，这种数字在数学上是无法定义的。NaN 一般是在没有意义的数学计算中得到的，比如 0/0，Inf/Inf，Inf-Inf，Inf*0。

另外，在处理不完整的数据时，这些数据也会被当做 NaN，比如在读取数字时，遇到的字符不是数字，就会被当做 NaN。

对 NaN 进行运算的结果仍然是 NaN，请看下面的演示代码：

```

>> a=[1 2 nan inf nan]
a =
    1    2   NaN   Inf   NaN

>> b=2*a
b =
    2    4   NaN   Inf   NaN

>> c=sqrt(a)
c =
    1.0000    1.4142    NaN    Inf    NaN

>> d=(a==nan)
d =
    0   0   0   0   0

>> f=(a~=nan)
f =
    1    1    1    1    1
```

上面例子中，第一条语句生成了一个含有 NaN 的向量 a，第二和第三条语句分别对 a 进行乘法和开方运算，从运算结果可以看到，对 NaN 进行数学运算的结果仍然是 NaN。

第四条和第五条语句的结果或许有些出乎意料：a==nan 得到的结果全部都是 0（false），而 a～=nan 却得到了全部是 1（true）的结果。这一结果表明，在 MATLAB 中，不同的 NaN 之间是不相等的。

鉴于 NaN 的这种特性，我们在进行关系运算时，就必须确定数组中是否含有 NaN。

## isnan() 函数

MATLAB 为我们提供了一个内置函数 isnan()，专门用来寻找数组中是否含有 NaN。下面的代码便是利用 isnan() 函数寻找 a 中的 NaN。

```

>> g=isnan(a)
g =
    0   0   1   0   1
```

由结果可知，isnan() 函数在数组中 NaN 的位置返回 1（true）。

另外，将 isnan() 和 find() 函数联合使用可以寻找数组中 NaN 的位置索引。例如，下面的代码为找到数组 a 中 NaN 的位置，然后在这些位置上用 0 替代 NaN。

```

>> i=find(isnan(a))  $查找 NaN 的索引位置
i =
     3     5

>> a(i)=zeros(size(i))  %将 NaN 更改为 zeros
a =
    1    2    0    Inf    0
```