# C#隐式数据类型转换

> 原文：[`c.biancheng.net/view/2840.html`](http://c.biancheng.net/view/2840.html)

在 C# 语言中隐式转换是指不需要其他方法数据类型直接即可转换。

隐式转换主要是在整型、浮点型之间的转换，将存储范围小的数据类型直接转换成存储范围大的数据类型。

例如将 int 类型的值转换成 double 类型的值，将 int 类型的值转换成 long 类型的值，或者将 float 类型的值转换成 double 类型的值。

示例代码如下。

```

int a = 100;
double d = a;  //将 int 类型转换为 double 类型
float f = 3.14f;
d = f;    //将 float 类型转换为 double 类型
```

隐式数值转换包括以下几种：

*   从 sbyte 类型到 short,int,long,float,double,或 decimal 类型。
*   从 byte 类型到 short,ushort,int,uint,long,ulong,float,double,或 decimal 类型。
*   从 short 类型到 int,long,float,double,或 decimal 类型。
*   从 ushort 类型到 int,uint,long,ulong,float,double,或 decimal 类型。
*   从 int 类型到 long,float,double,或 decimal 类型。
*   从 uint 类型到 long,ulong,float,double,或 decimal 类型。
*   从 long 类型到 float,double,或 decimal 类型。
*   从 ulong 类型到 float,double,或 decimal 类型。
*   从 char 类型到 ushort,int,uint,long,ulong,float,double,或 decimal 类型。
*   从 float 类型到 double 类型。

其中，从 int，uint 或 long 到 float 以及从 long 到 double 的转换可能会导致精度下降，但决不会引起数量上的丢失。其它的隐式数值转换则不会有任何信息丢失。

结合我们前面在《C#基本数据类型》中学习到的每种类型的取值范围，我们可以发现，隐式数值转换实际上就是从低精度的数值类型到高精度的数值类型的转换。