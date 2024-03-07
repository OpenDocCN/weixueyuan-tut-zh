# Java 计算平均成绩

编写一个程序，根据用户输入的总人数和总成缋计算平均成绩。要求程序能够处理总人数或者总成绩不是数字时的情况。

根据题目要求得知，在程序中需要对多种异常情况进行不同的处理。此时可以在 try 语句后定义两个或更多的 catch 语句块，其语法结构如下：

```
try
{
    逻辑代码块
}
catch(ExceptionType1 e1)
{
    处理代码块 1
}
catch(ExceptionType2 e2)
{
    处理代码块 2
}
…
catch(ExceptionTypen en)
{
    处理代码块 n
}
```

以上代码执行时，Java 虚拟机会把实际拋出的异常对象依次和各个 catch 代码块声明的异常类型匹配，如果异常对象为某个异常类型或者其子类的实例，就执行该 catch 代码块，而跳过其他的 catch 代码块。具体实现代码如下：

```
import Java.util.InputMismatchException;
import java.utii.Scanner;
public class TestO3
{
    public static void main(String[] args)
    {
        Scanner input=new Scanner(System.in);
        try
        {
            System.out.println("请输入班级总人数：");
            int count=input.nextInt();
            System.out.println("请输入总成绩：");
            int score=input.nextInt();
            int avg=score/count;    //获取平均分
            System.out.println("本次考试的平均分为："+avg);
        }
        catch(InputMismatchException e1)
        {
            System.out.println("输入数值有误！");
        }
        catch(ArithmeticException e2)
        {
            System.out.println("输入的总人数不能为 0！");
        }
        catch(Exception e3)
        {
            e3.printStackTrace();
            System.out.println("发生错误！"+e3.getMessage());
        }
    }
}
```

如上述代码，在 main() 方法中使用了多重 catch 语句来捕获各种可能发生的异常，包括 InputMismatchException 异常、ArithmeticException 异常以及其他类型的异常。

当用户输入的总人数或者总成绩不为数值类型时，程序将拋出 InputMismatchException 异常，从而执行 System.out.println("输入数值有误！"）代码，输出结果如下所示：

```
请输入班级总人数：
50
请输入总成绩：
1250a
输入数值有误！
```

```
请输入班级总人数：
50a
输入数值有误！
```

当输入的总人数为 0 计算平均成绩时会出现被除数为 0 的情况，此时会拋出 ArithmeticException 异常，从而执行 System.out.Println("输入的总人数不能为 0！")代码，输出结果如下所示：

```
请输入班级总人数：
0
请输入总成绩：
100
输入的总人数不能为 0！
```

如下所示的是当输入的总人数和总成绩均为正常数值类型时的输出结果：

```
请输入班级总人数：
50
请输入总成绩：
1250
本次考试的平均分为：25
```