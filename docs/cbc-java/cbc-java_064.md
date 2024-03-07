# Java 大数字运算（BigInteger 类和 BigDecimal 类）

在 Java 中提供了用于大数字运算的类，即 java.math.BigInteger 类和 java.math.BigDecimal 类。这两个类用于高精度计算，其中 BigInteger 类是针对整型大数字的处理类，而 BigDecimal 类是针对大小数的处理类。

## BigInteger 类

如果要存储比 Integer 更大的数字，Integer 数据类型就无能为力了。因此，Java 中提供 BigInteger 类来处理更大的数字。

BigInteger 类型的数字范围较 Integer 类型的数字范围要大得多。BigInteger 支持任意精度的整数，也就是说在运算中 BigInteger 类型可以准确地表示任何大小的整数值。

除了基本的加、减、乘、除操作之外，BigInteger 类还封装了很多操作，像求绝对值、相反数、最大公约数以及判断是否为质数等。

要使用 BigInteger 类，首先要创建一个 BigInteger 对象。BigInteger 类提供了很多种构造方法，其中最直接的一种是参数以字符串形式代表要处理的数字。这个方法语法格式如下：

```
BigInteger(String val)
```

这里的 val 是数字十进制的字符串。例如，要将数字 5 转换为 BigInteger 对象，语句如下：

```
BigInteger bi=new BigInteger("5")
```

注意：这里数字 5 的双引号是必需的，因为 BigInteger 类构造方法要求参数是字符串类型。

创建 BigInteger 对象之后，便可以调用 BigInteger 类提供的方法进行各种数学运算操作，表 1 列出了 BigInteger 类的常用运算方法。

表 1 BigInteger 类的常用运算方法

| 方法名称 | 说明 |
| add(BigInteger val) | 做加法运算 |
| subtract(BigInteger val) | 做减法运算 |
| multiply(BigInteger val) | 做乘法运算 |
| divide(BigInteger val) | 做除法运算 |
| remainder(BigInteger val) | 做取余数运算 |
| divideAndRemainder(BigInteger val) | 做除法运算，返回数组的第一个值为商，第二个值为余数 |
| pow(int exponent) | 做参数的 exponent 次方运算 |
| negate() | 取相反数 |
| shiftLeft(int n) | 将数字左移 n 位，如果 n 为负数，则做右移操作 |
| shiftRight(int n) | 将数字右移 n 位，如果 n 为负数，则做左移操作 |
| and(BigInteger val) | 做与运算 |
| or(BigInteger val) | 做或运算 |
| compareTo(BigInteger val) | 做数字的比较运算 |
| equals(Object obj) | 当参数 obj 是 Biglnteger 类型的数字并且数值相等时返回 true, 其他返回 false |
| min(BigInteger val) | 返回较小的数值 |
| max(BigInteger val) | 返回较大的数值 |

#### 例 1

编写一个 Java 程序，将用户输入的数字作为 BigInteger 对象，然后调用该对象的各种方法实现加、减、乘、除和其他运算，并输出结果。具体实现代码如下：

```
import java.math.BigInteger;
import java.util.Scanner;
public class Test09
{
    public static void main(String[] args)
    {
        Scanner input=new Scanner(System.in);
        System.out.println("请输入一个整型数字：");
        //保存用户输入的数字
        int num=input.nextInt();

        //使用输入的数字创建 BigInteger 对象
        Biglnteger bi=new BigInteger(num+"");

        //计算大数字加上 99 的结果
        System.out.println("加法操作结果："+bi.add(new BigInteger("99")));

        //计算大数字减去 25 的结果
        System.out.println("减法操作结果："+bi.subtract(new BigInteger("25")));

        //计算大数字乘以 3 的结果
        System.out.println("乘法橾作结果："+bi.multiply(new BigInteger("3")));

        //计算大数字除以 2 的结果
        System.out.println("除法操作结果："+bi.divide(new BigInteger("2")));

        //计算大数字除以 3 的商
        System.out.println("取商操作结果："+bi.divideAndRemainder(new BigInteger("3"))[0]);

        //计算大数字除以 3 的余数
        System.out.println("取余操作结果："+bi.divideAndRemainder(new BigInteger("3"))[1]);

        //计算大数字的 2 次方
        System.out.println("取 2 次方操作结果："+bi.pow(2));

        //计算大数字的相反数
        System.out.println("取相反数操作结果："+bi.negate());
    }
}
```

上述代码将用户输入的整型数字保存到 num 变量中，由于 BigInteger 类的构造方法只接收字符串类型的参数，所以使用“new BigInteger(num+"")” 代码来创建 BigInteger 对象。接下来的代码演示了如何调用 BigInteger 类提供的运算方法，运行效果下所示。

```
请输入一个整型数字：
125
加法操作结果：224
减法操作结果：100
乘法橾作结果：375
除法操作结果：62
取商操作结果：41
取余操作结果：2
取 2 次方操作结果：15625
取相反数操作结果：-125
```

## BigDecimal 类

BigInteger 和 BigDecimal 都能实现大数字的运算，不同的是 BigDecimal 加入了小数的概念。一般的 float 和 double 类型数据只能用来做科学计算或工程计算，但由于在商业计算中要求数字精度比较高，所以要用到 BigDecimal 类。BigDecimal 类支持任何精度的浮点数，可以用来精确计算货币值。

BigDecimal 常用的构造方法如下。

*   BigDecimal(double val)：实例化时将双精度型转换为 BigDecimal 类型。
*   BigDecimal(String val)：实例化时将字符串形式转换为 BigDecimal 类型。

BigDecimal 类的方法可以用来做超大浮点数的运算，像加、减、乘和除等。在所有运算中，除法运算是最复杂的，因为在除不尽的情况下，末位小数的处理方式是需要考虑的。

下面列出了 BigDecimal 类用于实现加、减、乘和除运算的方法。

```
BigDecimal add(BigDecimal augend)    //加法操作
BigDecimal subtract(BigDecimal subtrahend)    //减法操作
BigDecimal multiply(BigDecimal multiplieand)    //乘法操作
BigDecimal divide(BigDecimal divisor,int scale,int roundingMode )    //除法操作
```

其中，divide() 方法的 3 个参数分别表示除数、商的小数点后的位数和近似值处理模式。

表 2 列出了 roundingMode 参数支持的处理模式。

表 2 roundingMode 参数支持的处理模式

| 模式名称 | 说明 |
| BigDecimal.ROUND_UP | 商的最后一位如果大于 0，则向前进位，正负数都如此 |
| BigDecimal.ROUND_DOWN | 商的最后一位无论是什么数字都省略 |
| BigDecimal.ROUND_CEILING | 商如果是正数，按照 ROUND_UP 模式处理；如果是负数，按照 ROUND_DOWN 模式处理 |
| BigDecimal.ROUND_FLOOR | 与 ROUND_CELING 模式相反，商如果是正数，按照 ROUND_DOWN 模式处理； 如果是负数，按照 ROUND_UP 模式处理 |
| BigDecimal.ROUND_HALF_ DOWN | 对商进行五舍六入操作。如果商最后一位小于等于 5，则做舍弃操作，否则对最后 一位进行进位操作 |
| BigDecimal.ROUND_HALF_UP | 对商进行四舍五入操作。如果商最后一位小于 5，则做舍弃操作，否则对最后一位 进行进位操作 |
| BigDecimal.ROUND_HALF_EVEN | 如果商的倒数第二位是奇数，则按照 ROUND_HALF_UP 处理；如果是偶数，则按 照 ROUND_HALF_DOWN 处理 |

#### 例 2

编写一个 Java 程序，演示如何使用 BigDecimal 类提供的方法对数字执行运算，并输出结果。具体实现代码如下：

```
import java.math.BigDecimal;
import java.util.Scanner;
public class Test10
{
    public static void main(String[] args)
    {
        Scanner input=new Scanner(System.in);
        System.out.println("请输入一个数字：");

        //保存用户输入的数字
        double num=input.nextDouble();

        //使用输入的数字创建 BigDecimal 对象
        BigDecimal bd=new BigDecimal(num);

        //计算大数字加上 99.154 的结果
        System.out.println("加法操作结果："+bd.add(new BigDecimal(99.154)));

        //计算大数字减去-25.157904 的结果
        System.out.println("减法操作结果："+bd.subtract(new BigDecimal(-25.157904)));

        //计算大数字乘以 3.5 的结果
        System.out.println("乘法操作结果："+bd.multiply(new BigDecimal(3.5)));

        //计算大数字除以 3.14 的结果，并保留小数后 2 位
        System.out.println("除法操作结果(保留 2 位小数)："+bd.divide(new BigDecimal(3.14),2,BigDecimal.ROUND_CEILING));

        //计算大数字除以 3.14 的结果，并保留小数后 5 位
        System.out.println("除法操作结果(保留 5 位小数)："+bd.divide(new BigDecimal(3.14),5,BigDecimal.ROUND_CEILING));
    }
}
```

上述代码将用户输入的数字保存到 num 变量中，然后调用“newBigDecimal(num)”方法来创建 BigDecimal 对象。接下来的代码演示了如何调用 BigDecimal 类提供的运算方法，运行效果如下所示。

```
请输入一个数字：
100
加法操作结果：199.15399999999999636202119290828704833984375
减法操作结果：125.157903999999998490011421381495893001556396484375
乘法操作结果：350.0
除法操作结果(保留 2 位小数)：31.85
除法操作结果(保留 5 位小数)：31.84714
```