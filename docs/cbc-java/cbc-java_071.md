# Java Integer 类详解

Integer 类在对象中包装了一个基本类型 int 的值。Integer 类对象包含一个 int 类型的字段。此外，该类提供了多个方法，能在 int 类型和 String 类型之间互相转换，还提供了处理 int 类型时非常有用的其他一些常量和方法。

## Integer 类的构造方法

Integer 类中的构造方法有以下两个。

*   Integer(int value)：构造一个新分配的 Integer 对象，它表示指定的 int 值。
*   Integer(String s)：构造一个新分配的 Integer 对象，它表示 String 参数所指示的 int 值。

例如，以下代码分别使用以上两个构造方法来获取 Integer 对象：

```
Integer integer1=new Integer(100);    //以 int 型变量作为参数创建 Integer 对象
Integer integer2=new Integer("100");    //以 String 型变量作为参数创建 Integer 对象
```

## Integer 类的常用方法

在 Integer 类内部包含一些和 int 类型操作有关的方法，表 1 列出了这些常用的方法。

表 1 Integer 类中的常用方法

| 方法 | 返回值 | 功能 |
| byteValue() | byte | 以 byte 类型返回该 Integer 的值 |
| shortValue() | short | 以 short 类型返回该 Integer 的值 |
| intValue() | int | 以 int 类型返回该 Integer 的值 |
| toString() | String | 返回一个表示该 Intege r 值的 String 对象 |
| equals(Object obj) | boolean | 比较此对象与指定对象是否相等 |
| compareTo(Integer anotherlnteger) | int | 在数字上比较两个 Integer 对象，如相等，则返回 0； 如调用对象的数值小于 anotherlnteger 的数值，则返回负值；
如调用对象的数值大于 anotherlnteger 的数值，则返回正值 |
| valueOf(String s) | Integer | 返回保存指定的 String 值的 Integer 对象 |
| parseInt(String s) | int | 将数字字符串转换为 int 数值 |

在实际的编程过程中，经常将字符串转换为 int 类型的数值，或者将 int 类型的数值转换为对应的字符串。以下代码演示如何实现这两种功能：

```
String str="456";
int num=Integer.parseInt(str);    //将字符串转换为 int 类型的数值
int i=789;
String s=Integer.toString(i);    //将 int 类型的数值转换为字符串
```

注意：在实现将字符串转换为 int 类型数值的过程中，如果字符串中包含非数值类型的字符，则程序执行将出现异常。

#### 例 1

编写一个程序，在程序中创建一个 String 类型变量，然后将它转换为二进制、八进制、十进制和十六进制输出。

```
public class Test03
{
    public static void main(String[] args)
    {
        int num=40;
        String str=Integer.toString(num);    // 将数字转换成字符串
        String str1=Integer.toBinaryString(num);    // 将数字转换成二进制
        String str2=Integer.toHexString(num);    // 将数字转换成八进制
        String str3=Integer.toOctalString(num);    // 将数字转换成十六进制
        System.out.println(str+"的二进制数是："+str1);
        System.out.println(str+"的八进制数是："+str3);
        System.out.println(str+"的十进制数是；"+str);
        System.out.println(str+"的十六进制数是："+str2);
    }
}
```

运行后的输出结果如下：

```
40 的二进制数是：101000
40 的八进制数是：50
40 的十进制数是；40
40 的十六进制数是：28
```

## Integer 类的常量

Integer 类包含以下 4 个常量。

*   MAX_VALUE：值为 2³¹-1 的常量，它表示 int 类型能够表示的最大值。
*   MIN_VALUE：值为 -2³¹ 的常量，它表示 int 类型能够表示的最小值。
*   SIZE：用来以二进制补码形式表示 int 值的比特位数。
*   TYPE：表示基本类型 int 的 Class 实例。

下面的代码演示了 Integer 类中常量的使用。

```
int max_value=Integer.MAX_VALUE;    //获取 int 类型可取的最大值
int min_value=Integer.MIN_VALUE;    // 获取 int 类型可取的最小值
int size=Integer.SIZE;    // 获取 int 类型的二进制位
Class c=Integer.TYPE;    // 获取基本类型 int 的 Class 实例
```