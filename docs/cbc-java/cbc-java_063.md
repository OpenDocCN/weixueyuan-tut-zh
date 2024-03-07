# Java 数字格式化

数字的格式在解决实际问题时使用非常普遍，这时可以使用 DedmalFormat 类对结果进行格式化处理。例如，将小数位统一成 2 位，不足 2 位的以 0 补齐。

DecimalFormat 是 NumberFormat 的一个子类，用于格式化十进制数字。DecimalFormat 类包含一个模式和一组符号，常用符号的说明如表 1 所示。

表 1 DecimalFormat 支持的特殊字符

| 符号 | 说明 |
| 0 | 显示数字，如果位数不够则补 0 |
| # | 显示数字，如果位数不够不发生变化 |
| . | 小数分隔符 |
| - | 减号 |
| , | 组分隔符 |
| E | 分隔科学记数法中的尾数和小数 |
| % | 前缀或后缀，乘以 100 后作为百分比显示 |
| ? | 乘以 1000 后作为千进制货币符显示。用货币符号代替。如果双写，用国际货币符号代替； 如果出现在一个模式中，用货币十进制分隔符代替十进制分隔符 |

#### 例 1

下面编写一个 Java 程序，演示如何使用 DecimalFormat 类将数字转换成各种格式，实现代码如下。

```
import java.text.DecimalFormat;
import java.util.Scanner;
public class Test08
{
    public static void main(String[] args)
    {
        //实例化 DecimalFormat 类的对象，并指定格式
        DecimalFormat df1=new DecimalFormat("0.0");
        DecimalFormat df2=new DecimalFormat("#.#");
        DecimalFormat df3=new DecimalFormat("000.000");
        DecimalFormat df4=new DecimalFormat("###.###");
        Scanner scan=new Scanner(System.in);
        System.out.print("请输入一个 float 类型的数字：");
        float f=scan.nextFloat();
        //对输入的数字应用格式，并输出结果
        System.out.println("0.0 格式："+df1.format(f));
        System.out.println("#.# 格式："+df2.format(f));
        System.out.println("000.000 格式："+df3.format(f));
        System.out.println("###.### 格式："+df4.format(f));
    }
}
```

执行上述代码，输出结果如下所示：

```
请输入一个 float 类型的数字：5487.45697
0.0 格式：5487.5
#.# 格式：5487.5
000.000 格式：5487.457
###.### 格式：5487.457
```

```
请输入一个 float 类型的数字：5.0
0.0 格式：5.0
#.# 格式：5
000.000 格式：005.000
###.### 格式：5
```