# Java 连接字符串

对于已经定义的字符串，可以对其进行各种操作。连接多个字符串是字符串操作中最简单的一种。通过字符串连接，可以将两个或多个字符串、字符、整数和浮点数等类型的数据连成一个更大的字符串。

## 使用连接运算符

“+”运算符是最简单、最快捷，也是使用最多的字符串连接方式。在使用“+”运算符连接字符串和 int 型（或 double 型）数据时，“+”将 int（或 double）型数据自动转换成 String 类型。

#### 例 1

下面的实例使用“+”运算符连接了 3 个数组和 1 个字符串。

```
public static void main(String[] args)
{
    int[] no=new int[]{501,101,204,102,334};    //定义学号数组
    String[] names=new String[]{"张城","刘丽丽","李国旺","孟红霞","贺宁"};    //定义姓名数组
    String[] classes=newString[]{"数学","语文","数学","英语","英语"};    //定义课程数组
    System.out.println("本次考试学生信息如下：")；
    //循环遍历数组，连接字符串
    for(int i=0;i<no.length;i++)
    {
        System.out.println("学号："+no[i]+"|姓名："+names[i]+"|课程："+dasses[i]+"|班级："+"初二（三）班");
    }
}
```

上述代码首先创建了 3 个包含有 5 个元素的数组，然后循环遍历数组，遍历的次数为 5。在循环体内输出学号、姓名和课程，并使用“+”运算符连接班级最终形成一个字符串。程序运行后输出结果如下：

```
本次考试学生信息如下：
学号：501|姓名：张城|课程：数学|班级：初二（三）班
学号：101|姓名：刘丽丽|课程：语文丨班级：初二（三）班
学号：204|姓名：李国旺|课程：数学|班级：初二（三）班
学号：102|姓名：孟红霞|课程：英语|班级：初二（三）班
学号：334|姓名：贺宁|课程：英语|班级；初二（三）班
```

当定义的字符串值的长度过长时，可以分作多行来写，这样比较容易阅读。例如：

```
String str="Welcome to"+"Beijing"+"欢迎来到"+"北京。"+"北京我的故乡。";
```

## 使用 concat() 方法

在 Java 中，String 类的 concat() 方法实现了将一个字符串连接到另一个字符串的后面。concat() 方法语法格式如下：

```
字符串 1.concat(字符串 2);
```

执行结果是字符串 2 被连接到字符串 1 后面，形成新的字符串。

#### 例 2

如 concat() 方法的语法所示，concat() 方法一次只能连接两个字符串，如果需要连接多个字符串，需要调用多次 concat() 方法。

下面创建一个实例代码来演示如何使用 concat() 方法连接多个字符串。

```
public static void main(String[] args)
{
    String info="三国演义、";
    info=info.concat("西游记、");
    info=info.concat("水漭传、");
    info=info.concat("红楼梦");
    System.out.println(info);
    String cn="中国";
    System.out.println(cn.concat("北京").concat("海淀区").concat("人民公园"));
}
```

执行该段代码，输出的结果如下所示。

```
三国演义、西游记、水浒传、红楼梦
中国北京海淀区人民公园
```

## 连接其他类型数据

前面介绍的例子都是字符串与字符串进行连接，其实字符串也可同其他基本数据类型进行连接。如果将字符串同这些数据类型数据进行连接，此时会将这些数据直接转换成字符串。

#### 例 3

编写一个 Java 程序，实现将字符串与整型、浮点型变量相连并输出结果。实现代码如下：

```
public static void main(String[] args)
{
    String book="三国演义";    //字符串
    int price=59;    //整型变量
    float readtime=2.5f;    //浮点型变量
    System.out.println("我买了一本图书，名字是："+book+"\n 价格是："+price+"\n 我每天阅读"+readtime+"小时");
}
```

上述代码实现的是将字符串变量 book 与整型变量 price 和浮点型变量 readtime 相连后将结果输出。在这里定义的 price 和 readtime 都不是字符串，当它们与字符串相连时会自动调用自身的 toString() 方法将其转换成字符串形式，然后再参与连接运算。因此，程序运行后的结果如下所示：

```
我买了一本图书，名字是：三国演义
价格是：59
我每天阅读 2.5 小时
```

假设将本例中的输出语句修改为如下形式：

```
System.out.println("我买了一本图书，名字是："+book+"\n 价格是："+price+"\n 我每天阅读"+(price+readtime)+"小时");
```

因为运算符具有优先级，而圆括号的优先级最高，所以先计算 price 与 readtime 的和，再将结果转换成字符串进行连接。此时的运行结果如下所示：

```
我买了一本图书，名字是：三国演义
价格是：59
我每天阅读 61.5 小时
```

注意：只要“+”运算符的一个操作数是字符串，编译器就会将另一个操作数转换成字符串形式，所以应该谨慎地将其他数据类型与字符串相连，以免出现意想不到的结果。