# MATLAB 数组元素的排序

> 原文：[`c.biancheng.net/view/6746.html`](http://c.biancheng.net/view/6746.html)

MATLAB sort() 函数用来对数组元素进行排序，它有以下几种常见的用法：

B = sort(A)  %使用默认规则对数组排序
B = sort(A, dim)  %dim 指定排序的维度
B = sort(___, direction)  %direction 指定排序的方向

对参数和返回值的说明：

*   A 表示待排序的数组，B 表示排序后的数组。
*   dim 是指排序的维度，也即按列排序还是按行排序。dim=1（默认值）时按列排序，dim=2 时按行排序。
*   direction 表示排序的方向，也即升序还是降序。direction 为 'ascend' 时进行升序排序（默认），direction 为 'descend' 时进行降序排序。

___ 表示以上两种用法，比如你可以写 sort(A, direction)，也可以写 sort(A, dim, direction)。

sort() 支持排序的数据类型包括：double、single、int8、int16、int32、int64、uint8、uint16、uint32、uint64、logical、char、string、cell、categorical、datetime、duration 

【实例 1】对一维数组进行升序排序和降序排序。

```

>> A = [9 5 34 55 2 10 6 78 41]  %创建一维数组
A =
     9     5    34    55     2    10     6    78    41

>> B=sort(A)  %升序排序
B =
     2     5     6     9    10    34    41    55    78

>> B=sort(A, 'descend')  %降序排序
B =
    78    55    41    34    10     9     6     5     2
```

【实例 2】对二维数组元素进行排序。

```

>> A=magic(5)  %生成 5 阶幻方矩阵
A =
    17    24     1     8    15
    23     5     7    14    16
     4     6    13    20    22
    10    12    19    21     3
    11    18    25     2     9

>> B=sort(A)  %按列升序排序
B =
     4     5     1     2     3
    10     6     7     8     9
    11    12    13    14    15
    17    18    19    20    16
    23    24    25    21    22

>> B=sort(A, 2)  %按行升序排序
B =
     1     8    15    17    24
     5     7    14    16    23
     4     6    13    20    22
     3    10    12    19    21
     2     9    11    18    25

>> B=sort(A, 2, 'descend')  %按行降序排序
B =
    24    17    15     8     1
    23    16    14     7     5
    22    20    13     6     4
    21    19    12    10     3
    25    18    11     9     2
```

【实例 3】对字符串数组进行排序。

```

>> A=["MATLAB", "Java", "Python", "C++", "PHP", "JavaScript", "Shell"]

>> B=sort(A)
B =
    "C++"    "Java"    "JavaScript"    "MATLAB"    "PHP"    "Python"    "Shell"
```

从 MATLAB R2017a 开始，sort() 函数支持对字符串数组的排序，它会根据 Unicode 编码顺序对字符串的字符进行排序。