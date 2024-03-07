# Java Math 类的常用方法详解

在 Java 中 Math 类封装了常用的数学运算，提供了基本的数学操作，如指数、对数、平方根和三角函数等。Math 类位于 java.lang 包，下面详细介绍该类的常量及数学处理方法。

## 静态常量

Math 类中包含 E 和 PI 两个静态常量，其中 E 用于记录 e 的常量，而 PI 用于记录圆周率的值。

#### 例 1

调用 Math 类的 E 和 PI 两个常量，并将结果输出。代码如下：

```
System.out.println("E 常量的值："+Math.E);
System.out.println("PI 常量的值："+Math.PI);
```

执行上述代码，输出结果如下：

```
E 常量的值：2.718281828459045
PI 常量的值：3.141592653589793
```

## 求最大值、最小值和绝对值

在程序中常见的就是求最大值、最小值和绝对值问题，如果使用 Math 类提供的方法可以很容易实现。这些方法的说明如表 1 所示。

表 1 求最大值、最小值和绝对值的方法

| 方法 | 说明 |
| static int abs(int a) | 返回 a 的绝对值 |
| static long abs(long a) | 返回 a 的绝对值 |
| static float abs(float a) | 返回 a 的绝对值 |
| static double abs(double a) | 返回 a 的绝对值 |
| static int max(int x,int y) | 返回 x 和 y 中的最大值 |
| static double max(double x,double y) | 返回 x 和 y 中的最大值 |
| static long max(long x,long y) | 返回 x 和 y 中的最大值 |
| static float max(float x,float y) | 返回 x 和 y 中的最大值 |
| static int min(int x,int y) | 返回 x 和 y 中的最小值 |
| static long min(long x,long y) | 返回 x 和 y 中的最小值 |
| static double min(double x,double y) | 返回 x 和 y 中的最小值 |
| static float min(float x,float y) | 返回 x 和 y 中的最小值 |

#### 例 2

求 10 和 20 的较大值、15.6 和 15 的较小值、-12 的绝对值，代码如下：

```
public class Test02
{
    public static void main(String[] args)
    {
        System.out.println("10 和 20 的较大值："+Math.max(10, 20));
        System.out.println("15.6 和 15 的较小值："+Math.min(15.6,15));
        System.out.println("-12 的绝对值："+Math.abs(-12));
    }
}
```

该程序的运行结果如下：

```
10 和 20 的较大值：20
15.6 和 15 的较小值：15.0
-12 的绝对值：12
```

## 求整运算

Math 类的求整方法有很多，详细说明如表 2 所示。

表 2 取整方法及其说明

| 方法 | 说明 |
| static double ceil(double a) | 返回大于或等于 a 的最小整数 |
| static double floor(double a) | 返回小于或等于 a 的最大整数 |
| static double rint(double a) | 返回最接近 a 的整数值，如果有两个同样接近的整数，则结果取偶数 |
| static int round(float a) | 将参数加上 1/2 后返回与参数最近的整数 |
| static long round(double a) | 将参数加上 1/2 后返回与参数最近的整数，然后强制转换为长整型 |

#### 例 3

下面的实例演示了 Math 类中取整函数方法的应用：

```
import java.util.Scanner;
public class Test03
{
    public static void main(String[] args)
    {
        Scanner input=new Scanner(System.in);
        System.outprintln("请输入一个数字：");
        double num=input.nextDouble();
        System.out.println("大于或等于 "+num+" 的最小整数："+Math.ceil(num));
        System.out.println("小于或等于 "+num+" 的最大整数："+Math.floor(num));
        System.out.println("将 "+num+" 加上 0.5 之后最接近的整数："+Math.round(num));
        System.out.println("最接近 "+num+" 的整数："+Math.rint(num));
    }
}
```

执行结果如下：

```
请输入一个数字：
99.01
大于或等于 99.01 的最小整数：100.0
小于或等于 99.01 的最大整数：99.0
将 99.01 加上 0.5 之后最接近的整数：100
最接近 99.01 的整数：99.0
```

## 三角函数运算

Math 类中包含的三角函数方法及其说明如表 3 所示。

表 3 三角函数方法及其说明

| 方法 | 说明 |
| static double sin(double a) | 返回角的三角正弦值，参数以孤度为单位 |
| static double cos(double a) | 返回角的三角余弦值，参数以孤度为单位 |
| static double asin(double a) | 返回一个值的反正弦值，参数域在 [-1,1]，值域在 [-PI/2,PI/2] |
| static double acos(double a) | 返回一个值的反余弦值，参数域在 [-1,1]，值域在 [0.0,PI] |
| static double tan(double a) | 返回角的三角正切值，参数以弧度为单位 |
| static double atan(double a) | 返回一个值的反正切值，值域在 [-PI/2,PI/2] |
| static double toDegrees(double angrad) | 将用孤度表示的角转换为近似相等的用角度表示的角 |
| staticdouble toRadians(double angdeg) | 将用角度表示的角转换为近似相等的用弧度表示的角 |

在表 3 中，每个方法的参数和返回值都是 double 类型，参数以弧度代替角度来实现，其中 1 度等于 π/180 弧度，因此平角就是 π 弧度。

#### 例 4

计算 90 度的正弦值、0 度的余弦值、1 的反正切值、120 度的弧度值，代码如下：

```
public class Test04
{
    public static void main(String[] args)
    {
        System.out.println{"90 度的正弦值："+Math.sin(Math.PI/2));
        System.out.println("0 度的余弦值："+Math.cos(0));
        System.out.println("1 的反正切值："+Math.atan(l));
        System.out.println("120 度的弧度值："+Math.toRadians(120.0));
    }
}
```

在上述代码中，因为 Math.sin() 中的参数的单位是弧度，而 90 度表示的是角度，因此需要将 90 度转换为弧度，即 Math.PI/180*90，故转换后的弧度为 Math.PI/2，然后调用 Math 类中的 sin() 方法计算其正弦值。

该程序的运行结果如下：

```
90 度的正弦值：1.0
0 的余弦值：1.0
1 的反正切值：0.7853981633974483
120 度的弧度值：2.0943951023931953
```

## 指数运算

指数的运算包括求方根、取对数及其求 n 次方的运算。在 Math 类中定义的指数运算方法及其说明如表 4 所示。

表 4 指数方法及其说明

| 方法 | 说明 |
| static double exp(double a) | 返回 e 的 a 次幂 |
| static double pow(double a,double b) | 返回以 a 为底数，以 b 为指数的幂值 |
| static double sqrt(double a) | 返回 a 的平方根 |
| static double cbrt(double a) | 返回 a 的立方根 |
| static double log(double a) | 返回 a 的自然对数，即 lna 的值 |
| static double log10(double a) | 返回以 10 为底 a 的对数 |

#### 例 5

使用 Math 类中的方法实现指数的运算，main() 方法中的代码如下：

```
public class Test05
{
    public static void main(String[] args)
    {
        System.out.println("4 的立方值："+Math.pow(4, 3));
        System.out.println("16 的平方根："+Math.sqrt(16));
        System.out.println("10 为底 2 的对数："+Math.log1O(2));
    }
}
```

该程序的运行结果如下：

```
4 的立方值：64.0
16 的平方根：4.0
10 为底 2 的对数：0.3010299956639812
```