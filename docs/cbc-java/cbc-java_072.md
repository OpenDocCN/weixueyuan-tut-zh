# Java Float 类

Float 类在对象中包装了一个基本类型 float 的值。Float 类对象包含一个 float 类型的字段。此外，该类提供了多个方法，能在 float 类型与 String 类型之间互相转换，同时还提供了处理 float 类型时比较常用的常量和方法。

## Float 类的构造方法

Float 类中的构造方法有以下 3 个。

*   Float(double value)：构造一个新分配的 Float 对象，它表示转换为 float 类型的参数。
*   Float(float value)：构造一个新分配的 Float 对象，它表示基本的 float 参数。
*   Float(String s)：构造一个新分配的 Float 对象，它表示 String 参数所指示的 float 值。

例如，以下代码分别使用以上 3 个构造方法获取 Float 对象：

```
Float float1=new Float(3.14145);    //以 double 类型的变量作为参数创建 Float 对象
Float float2=new Float(6.5);    //以 float 类型的变量作为参数创建 Float 对象
Float float3=new Float("3.1415");    //以 String 类型的变量作为参数创建 Float 对象
```

在 Float 类内部包含了一些和 float 操作有关的方法，见表 1。

表 1 Float 类中的常用方法

| 方法 | 返回值 | 功能 |
| byteValue() | byte | 以 byte 类型返回该 Float 的值 |
| doubleValue() | double | 以 double 类型返回该 Float 的值 |
| floatValue() | float | 以 float 类型返回该 Float 的值 |
| intValue() | int | 以 int 类型返回该 Float 的值（强制转换为 int 类型） |
| longValue() | long | 以 long 类型返回该 Float 的值（强制转换为 long 类型） |
| shortValue() | short | 以 short 类型返回该 Float 的值（强制转换为 short 类型） |
| isNaN() | boolean | 如果此 Float 值是一个非数字值，则返回 true，否则返回 false |
| isNaN(float v) | boolean | 如果指定的参数是一个非数字值，则返回 true，否则返回 false |
| toString() | String | 返回一个表示该 Float 值的 String 对象 |
| valueOf(String s) | Float | 返回保存指定的 String 值的 Float 对象 |
| parseFloat(String s) | float | 将数字字符串转换为 float 数值 |

例如，将字符串 456.7 转换为 float 类型的数值，或者将 float 类型的数值 123.4 转换为对应的字符串，以下代码演示如何实现这两种功能：

```
String str="456.7";
float num=Float.parseFloat(str);    //将字符串转换为 float 类型的数值
float f=123.4f;
String s=Float.toString(f);    //将 float 类型的数值转换为字符串
```

注意：在实现将字符串转换为 float 类型数值的过程中，如果字符串中包含非数值类型的字符，则程序执行将出现异常。

## Float 类的常用常量

在 Float 类中包含了很多常量，其中较为常用的常量如下。

*   MAX_VALUE：值为 1.4E38 的常量，它表示 float 类型能够表示的最大值。
*   MIN_VALUE：值为 3.4E-45 的常量，它表示 float 类型能够表示的最小值。
*   MAX_EXPONENT:有限 float 变量可能具有的最大指数。
*   MIN_EXPONENT：标准化 float 变量可能具有的最小指数。
*   MIN_NORMAL：保存 float 类型数值的最小标准值的常量，即 2^(-126^。)
*   NaN：保存 float 类型的非数字值的常量。
*   SIZE：用来以二进制补码形式表示 float 值的比特位数。
*   TYPE：表示基本类型 float 的 Class 实例。

下面的代码演示了 Float 类中常量的使用。

```
float max_value=Float.MAX_VALUE;    //获取 float 类型可取的最大值
float min_value=Float.MIN_VALUE;    //获取 float 类型可取的最小值
float min_normal=Float.MIN_NORMAL;    //获取 float 类型可取的最小标准值
float size=Float.SIZE;    //获取 float 类型的二进制位
```