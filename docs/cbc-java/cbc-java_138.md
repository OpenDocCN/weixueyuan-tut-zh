# Java try catch 语句

在 Java 中通常采用 try catch 语句来捕获异常并处理。语法格式如下：

```
try
{
    逻辑代码块 1;
}
catch(ExceptionType e)
{
    处理代码块 1;
}
```

在以上语法中，把可能引发异常的语句封装在 try 语句块中，用以捕获可能发生的异常。

如果 try 语句块中发生异常，那么一个相应的异常对象就会被拋出，然后 catch 语句就会依据所拋出异常对象的类型进行捕获，并处理。处理之后，程序会跳过 try 语句块中剩余的语句，转到 catch 语句块后面的第一条语句开始执行。

如果 try 语句块中没有异常发生，那么 try 块正常结束，后面的 catch 语句块被跳过，程序将从 catch 语句块后的第一条语句开始执行。

在以上语法的处理代码块 1 中，可以使用以下 3 个方法输出相应的异常信息。

*   printStackTrace() 方法：指出异常的类型、性质、栈层次及出现在程序中的位置。
*   getMessage() 方法：输出错误的性质。
*   toString() 方法：给出异常的类型与性质。

注意：catch 语句的参数类似于方法的声明，包括一个异常类型和一个异常对象。异常类型必须为 Throwable 类的子类，它指明 catch 语句可以处理的异常类型，异常对象则由运行时系统在 try 语句块中生成并被捕获。

#### 例 1

编写一个录入学生姓名、年龄和性别的程序，要求能捕捉年龄不为数字时的异常。在这里使用 try catch 语句来实现，具体代码如下：

```
import java.util.Scanner;
public class Test02
{
    public static void main(String[] args)
    {
        Scanner scanner=new Scanner(System.in);
        System.out.println("---------学生信息录入---------------");
        String name="";    //获取学生姓名
        int age=0;    //获取学生年龄
        String sex="";    //获取学生性别
        try
        {
            System.out.println("请输入学生姓名：");
            name=scanner.next();
            System.out.println("请输入学生年龄：");
            age=scanner.nextInt();
            System.out.println("请输入学生性别：");
            sex=scanner.next();
        }
        catch(Exception e)
        {
            e.printStackTrace();
            System.out.println("输入有误！");
        }
        System.out.println("姓名："+name);
        System.out.println("年龄："+age);
    }
}
```

上述代码在 main() 方法中使用 try catch 语句来捕获异常，将可能发生异常的“age=scanner.nextlnt();”代码放在了 try 块中，在 catch 语句中指定捕获的异常类型为 Exception，并调用异常对象的 printStackTrace() 方法输出异常信息。运行结果如下所示。

```
---------学生信息录入---------------
请输入学生姓名：
徐白
请输入学生年龄：
110a
java.util.InputMismatchException
    at java.util.Scanner.throwFor(Unknown Source)
    at java.util.Scanner.next(Unknown Source)
    at java.util.Scanner.nextInt(Unknown Source)
    at java.util.Scanner.nextInt(Unknown Source)
输入有误！
姓名：徐白
年龄：0
    at text.text.main(text.java:19)
```

提示：一个 catch 语句也可以捕捉多个异常类型，这时它的异常类型参数应该是这多个异常类型的父类。程序设计中要根据具体的情况来选择 catch 语句的异常处理类型。