# Java Double 类

Double 类在对象中包装了一个基本类型 double 的值。Double 类对象包含一个 double 类型的字段。此外，该类还提供了多个方法，可以将 double 类型与 String 类型相互转换，同时 还提供了处理 double 类型时比较常用的常量和方法。

## Double 类的构造方法

Double 类中的构造方法有如下两个。

*   Double(double value)：构造一个新分配的 Double 对象，它表示转换为 double 类型的参数。
*   Double(String s)：构造一个新分配的 Double 对象，它表示 String 参数所指示的 double 值。

例如，以下代码分别使用以上两个构造方法获取 Double 对象：

```
Double double1=new Double(5.456);    //以 double 类型的变量作为参数创建 Double 对象
Double double2=new Double("5.456");       //以 String 类型的变量作为参数创建 Double 对象
```

## Double 类的常用方法

在 Double 类内部包含一些和 double 操作有关的方法，见表 1。

表 1 Double 类中的常用方法

| 方法 | 返回值 | 功能 |
| byteValue() | byte | 以 byte 类型返回该 Double 的值 |
| doubleValue() | double | 以 double 类型返回该 Double 的值 |
| fioatValue() | float | 以 float 类型返回该 Double 的值 |
| intValue() | int | 以 int 类型返回该 Double 的值（强制转换为 int 类型） |
| longValue() | long | 以 long 类型返回该 Double 的值（强制转换为 long 类型） |
| shortValue() | short | 以 short 类型返回该 Double 的值（强制转换为 short 类型） |
| isNaN() | boolean | 如果此 Double 值是一个非数字值，则返回 true，否则返回 false |
| isNaN(double v) | boolean | 如果指定的参数是一个非数字值，则返回 true，否则返回 false |
| toString() | String | 返回一个表示该 Double 值的 String 对象 |
| valueOf(String s) | Double | 返回保存指定的 String 值的 Double 对象 |
| parseDouble(String s) | double | 将数字字符串转换为 Double 数值 |

例如，将字符串 56.7809 转换为 double 类型的数值，或者将 double 类型的数值 56.7809 转换为对应的字符串，以下代码演示如何实现这两种功能：

```
String str="56.7809";
double num=Double.parseDouble(str);    //将字符串转换为 double 类型的数值
double d=56.7809;
String s=Double.toString(d);    //将 double 类型的数值转换为字符串
```

在将字符串转换为 double 类型的数值的过程中，如果字符串中包含非数值类型的字符，则程序执行将出现异常。

## Double 类的常用常量

在 Double 类中包含了很多常量，其中较为常用的常量如下。

*   MAX_VALUE:值为 1.8E308 的常量，它表示 double 类型的最大正有限值的常量。
*   MIN_VALUE：值为 4.9E-324 的常量，它表示 double 类型数据能够保持的最小正非零值的常量。
*   NaN：保存 double 类型的非数字值的常量。
*   NEGATIVE_INFINITY：保持 double 类型的负无穷大的常量。
*   POSITIVE_INFINITY：保持 double 类型的正无穷大的常量。
*   SIZE：用秦以二进制补码形式表示 double 值的比特位数。
*   TYPE：表示基本类型 double 的 Class 实例。