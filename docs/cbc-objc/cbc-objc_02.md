# OC 中用到的 C 语言知识

OC 由 C 语言拓展而来，这节讲解 OC 编程中常用的 C 语言的知识，以及它们的差别。

## 基本数据类型

在 OC 中经常会用到 C 语言的基本数据类型，例如 short、int、long、float、double 等，它们的长度如下所示：

| 数据类型 | short | int | long | float | double |
| 名称 | 短整型 | 整型 | 长整型 | 单精度浮点型 | 双精度浮点型 |
| 长度 | 2 | 4 | 8 | 4 | 8 |

不过，OC 语言有自己的字符串和数组类型，一般很少用 C 语言中的 char 和数组。

## 分支和循环结构

OC 语言的分支和循环结构的语法和 C 语言相同，分支结构使用`if...else`和`switch...case`，循环结构使用`while`、`do...while`、`for`，如下所示：

```
int a = 10, b = 20, i, n = 100, total = 0;

//输出最大值
if(a<b){
    printf("The max is %d.\n", b);
}else{
    printf("The max is %d.\n", a);
}

//计算 1+2+3+...+99+100 的值
for(i=1; i<=n; i++){
    total += i;
}
printf("1+2+3+...+99+100 = %d\n", total);
```

另外，OC 还增加了一种循环格式，就是 for...in 循环，它的使用和 C 语言中的 for 非常类似，后续我们会介绍。