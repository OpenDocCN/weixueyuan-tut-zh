# Java 完善除法运算的错误信息

本次实例的目的很简单，就是由用户输入两个数字，然后进行相除并输出结果。但是要求程序对除法运算过程中的各种异常进行捕捉并提示用户，例如除数是字符、被除数是零等。

在这个实例中定义了一个数组来存放要执行运算的两个操作数，因此还需要对数组的索引越界异常进行处理，具体步骤如下。

(1) 创建计算类 Compute，声明一个长度为 2 的 int 类型数组，接着编写 get() 和 set() 方法， 代码如下：

```
public class Compute
{
    private int[] num=new int[2];
    public int[] getNum()
    {
        return num;
    }
    public void setNum(int[] num)
    {
        this.num=num;
    }
}
```

(2) 在 Compute 类的 main() 方法中编写测试代码。定义一个 int 类型的变量 res 来接收计算的结果，将从控制台接收到的 int 类型的数据存储到数组中，如果输入 Y 表示将计算的结果存储到数组下标为 2 的位置，接着在控制台输出结果。代码如下：

```
Compute c=new Compute();
int array[]=c.getNum();
int res=0;
String YorN=null;
Scanner in=new Scanner(System.in);
try
{
    System.out.println("请输入第一个整数：");
    array[0]=in.nextInt();
    System.out.println("请输入第二个整数：");
    array[1]=in.nextInt();
    res=array[0]/array[1];
    System.out.println("是否保存结果请输入 Y 或者 N");
    YorN=in.next();
    if(YorN.equals("Y"))
    {
        array[2]=res;
    }
    System.out.println(array[0]+"除以"+array[1]+"的结果是："+res);
}
```

(3) 使用 catch 分别捕获可能出现的异常，代码如下。

```
catch(ArrayIndexOutOfBoundsException e)
{
    System.out.println("出现数组越界错误，下标过大或者过小。");
}
catch(ArithmeticException e)
{
    System.out.println("出现算术运算错误，被除数不能为 0。");

}
catch(InputMismatchException e)
{
    System.out.println("输入的数据类型不匹配，只能输入数字。");
}
catch(Exception e)
{
    System.out.println("发生未知错误。");
}
```

(4) 运行程序，先输入一个整数，接着第二个数输入 0，此时程序发生算术运算异常，异常被异常类型为 ArithmeticException 的 catch 语句块捕获，控制台会输出“出现算术运算错误，被除数不能为 0。”，执行效果如下所示。

```
请输入第一个整数：
100
请输入第二个整数：
0
出现算术运算错误，被除数不能为 0。
```

(5) 重新运行程序，先输入一个整数，接着第二个数中输入一个字符，此时程序发生类型不匹配异常，异常被异常类型为 InputMismatchException 的 catch 语句块捕获，控制台会输出“输入的数据类型不匹配，只能输入数字。”，执行效果如下所示。

```
请输入第一个整数：
100
请输入第二个整数：
9t
输入的数据类型不匹配，只能输入数字。
```

(6) 继续运行程序，输入两个整数，然后控制台输出“是否保存结果请输入 Y 或者 N”。此时输入“Y”，程序发生数组越界异常，因为长度为 2 的数组，其下标最大为 1。将结果保存在 army[2] 时出现数组越界错误，控制台输出“出现数组越界错误，下标过大或者过小。”。执行效果如下所示。

```
请输入第一个整数：
100
请输入第二个整数：
10
是否保存结果请输入 Y 或者 N
Y
出现数组越界错误，下标过大或者过小。
```

(7) 再次运行程序，输入两个整数。当控制台输出“是否保存结果请输入 Y 或者 N”时，输入“N”，表示不保存运行结果，也就不需要对下标为 2 的地址空间的访问，那么不会发生数组越界异常，程序可以顺序执行到 try 语句块中的最后一个输出语句。执行效果如下所示。

```
请输入第一个整数：
100
请输入第二个整数：
20
是否保存结果请输入 Y 或者 N
N
100 除以 20 的结果是：5
```