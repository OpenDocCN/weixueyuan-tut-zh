# Java try catch finally 语句

在实际开发中，根据 try catch 语句的执行过程，try 语句块和 catch 语句块有可能不被完全执行，而有些处理代码则要求必须执行，例如文件的关闭、释放资源等，此时就可以将这些代码放在 finally 语句块中。

finally 语句可以与前面介绍的 [try catch](http://c-local.biancheng.net/view/1044.html) 语句块匹配使用，语法格式如下：

```
try
{
    逻辑代码块
}
catch(ExceptionType e)
{
    异常处理代码块
}
finally
{
    清理代码块
}
```

对于以上格式，无论是否发生异常（除特殊情况外），finally 语句块中的代码都会被执行。此外，finally 语句也可以和 try 语句匹配使用，其语法格式如下：

```
try
{
    逻辑代码块
}
finally
{
    清理代码块
}
```

提示：finally 与 try 语句块匹配的语法格式，此种情况会导致异常丢失，所以不常见。

一般情况下，无论是否有异常拋出，都会执行 finally 语句块中的语句，执行流程如图 1 所示。

![](img/fed0c868b8fef8c82b2dfcdcc8b52bf2.jpg)
图 1 try catch finally 语句执行流程图
try catch finally 语句块的执行情况可以细分为以下 5 种情况：

1.  如果 try 代码块中没有拋出异常，则执行完 try 代码块之后直接执行 finally 代码块，然后执行 try catch finally 语句块之后的语句。
2.  如果 try 代码块中拋出异常，并被 catch 子句捕捉，那么在拋出异常的地方终止 try 代码块的执行，转而执行相匹配的 catch 代码块，之后执行 finally 代码块。如果 finally 代码块中没有拋出异常，则继续执行 try catch finally 语句块之后的语句；如果 finally 代码块中拋出异常，则把该异常传递给该方法的调用者。
3.  如果 try 代码块中拋出的异常没有被任何 catch 子句捕捉到，那么将直接执行 finally 代码块中的语句，并把该异常传递给该方法的调用者。
4.  在前面的代码中用 System.exit() 退出运行。如果代码在 try 内部执行一条 System.exit() 语句，则应用程序将终止而不会执行 finally。
5.  如果在执行 finally 块之前，程序所在的线程死亡，finally 块将不被执行。

**【例 1】**当 Windows 系统启动之后，即使不作任何操作，在关机时都会显示“谢谢使用”。下面编写 Java 程序使用 try catch finally 语句这个过程，具体代码如下：

```
public class Test04
{
    public static void main(String[] args)
    {
        Scanner input=new Scanner(System.in);
        System.out.println("Windows 系统已启动！");
        String[] pros={"记事本","计算器","浏览器"};
        try
        {
            //循环输出 pros 数组中的元素
            for(int i=0;i<pros.length;i++)
            {
                System.out.println(i+1+"："+pros[i]);
            }
            System.out.println("是否运行程序：");
            String answer=input.next();
            if(answer.equals("y"))
            {
                System.out.println("请输入程序编号：");
                int no=input.nextInt();
                System.out.println("正在运行程序["+pros[no-1]+"]");
            }
        }
        catch(Exception e)
        {
            e.printStackTrace();
        }
        finally
        {
            System.out.println("谢谢使用!");
        }
    }
}
```

上述代码在 main() 方法中使用 try catch finally 语句模拟了系统的使用过程。当系统启动之后显示提示语，无论是否运行了程序，或者在运行程序时出现了意外，程序都将执行 finally 块中的语句，即显示“谢谢使用！”。输出时的结果如下所示。

```
Windows 系统已启动！
1：记事本
2：计算器
3：浏览器
是否运行程序：
y
请输入程序编号：
2
正在运行程序[计算器]
谢谢使用!
```

```
Windows 系统已启动！
1：记事本
2：计算器
3：浏览器
是否运行程序：
y
请输入程序编号：
5
谢谢使用!
java.lang.ArrayIndexOutOfBoundsException: 4
    at text.text.main(text.java:23)
```

```
Windows 系统已启动！
1：记事本
2：计算器
3：浏览器
是否运行程序：
asdfasd
谢谢使用!
```