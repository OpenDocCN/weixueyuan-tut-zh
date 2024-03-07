# Java return 语句

return 语句用于终止函数的执行或退出类的方法，并把控制权返回该方法的调用者。如果这个方法带有返回类型，return 语句就必须返回这个类型的值；如果这个方法没有返回值，可以使用没有表达式的 return 语句。

return 语句的一般语法格式如下：

```
return 与方法相同类型的变量;
```

如果一个方法使用了 return 语句并且后面跟有该方法返回类型的值，那么调用此方法后，所得到的结果为该方法返回的值。

#### 例 1

当用户输入两个 double 类型的数值后需要计算这两个数值之和，而这两个操作数是可以变动的，但是求和的功能是不变的，这时就需要定义一个方法，只要在需要求和时调用该方法即可，该方法将计算之后的结果返回。代码如下：

```
import java.util.Scanner;
public class Test23
{
    public static void main(String[] args)
    {
        Scanner input=new Scanner(System.in);
        System.out.println("请输入操作数 1：");
        double num1=input.nextDouble();    //获取用户输入的操作数 1
        System.out.println("请输入操作数 2：");
        double num2=input.nextDouble();    //获取用户输入的操作数 2
        double d=sum(num1,num2);
        System.out.println(num1+"+"+num2+"="+d);
    }
    /**
    * 创建 sum() 方法，返回 double 类型的数值
    * @param i 操作数 1
    * @param j 操作数 2
    * @return 两个操作数之和
    */
    public static double sum(double i,double j)
    {
        double sum=i+j;
        return sum;
    }
}
```

当用户输入两个 double 类型的数值后，程序调用 sum() 方法。该方法有两个参数，分别表示用户输入的操作数 1 和操作数 2。在调用该方法时，只需要将用户输入的两个数值传递过去即可，然后程序会执行 sum() 方法，对这两个数求和，并使用 return 语句将计算得出的结果返回。

在 main() 方法中，同样需要使用 double 类型的变量来接收 sum() 方法返回的值，即变量 d 为计算后的结果。

运行后的结果如下所示：

```
请输入操作数 1：
500
请输入操作数 2：
203
500.0+203.0=703.0
```

```
请输入操作数 1：
5.66
请输入操作数 2：
3.0158
5.66+3.0158=8.6758
```