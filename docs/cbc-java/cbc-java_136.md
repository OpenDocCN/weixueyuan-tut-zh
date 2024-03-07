# Java 中的异常（Exception）：什么是异常？异常产生的原因及常见的几种异常类型

Java 中的异常（Exception）又称为例外，是一个在程序执行期间发生的事件，它中断正在执行的程序的正常指令流。为了能够及时有效地处理程序中的运行错误，必须使用异常类。

## 异常简介

在程序中，错误可能产生于程序员没有预料到的各种情况，或者超出程序员可控范围的环境，例如用户的坏数据、试图打开一个不存在的文件等。为了能够及时有效地处理程序中的运行错误，Java 专门引入了异常类。

#### 例 1

为了更好地理解什么是异常，下面来看一段非常简单的 Java 程序。下面的示例代码实现了允许用户输入 1~3 以内的整数，其他情况提示输入错误。

```
package ch11;
import Java.util.Scanner;
public class TestO1
{
    public static void main(String[] args)
    {
        System.out.println("请输入您的选择：（1~3 之间的整数）");
        Scanner input=new Scanner(System.in);
        int num=input.nextInt();
        switch(num)
        {
            case 1:
                System.out.println("one");
                break;
            case 2:
                System.out.println("two");
                break;
            case 3:
                System.out.println("three");
                break;
            default:
                System.out.println("error");
                break;
        }
    }
}
```

正常情况下，用户会按照系统的提示输入 1~3 之间的数字。但是，如果用户没有按要求进行输入，例如输入了一个字母“a”，则程序在运行时将会发生异常，运行结果如下所示。

```
请输入您的选择：（1~3 之间的整数）
a
Exception in thread "main" java.util.InputMismatchException
at java.util.Scanner.throwFor(Unknown Source)
at java.util.Scanner.next(Unknown Source)
at java.util.Scanner.nextInt(Unknown Source)
at java.util.Scanner.nextInt(Unknown Source)
at text.text.main(text.java:11)
```

## 异常产生的原因及使用原则

在 Java 中一个异常的产生，主要有如下三种原因：

1.  Java 内部错误发生异常，Java 虚拟机产生的异常。
2.  编写的程序代码中的错误所产生的异常，例如空指针异常、数组越界异常等。这种异常称为未检査的异常，一般需要在某些类中集中处理这些异常。
3.  通过 throw 语句手动生成的异常，这种异常称为检査的异常，一般用来告知该方法的调用者一些必要的信息。

Java 通过面向对象的方法来处理异常。在一个方法的运行过程中，如果发生了异常，则这个方法会产生代表该异常的一个对象，并把它交给运行时的系统，运行时系统寻找相应的代码来处理这一异常。

我们把生成异常对象，并把它提交给运行时系统的过程称为拋出（throw）异常。运行时系统在方法的调用栈中查找，直到找到能够处理该类型异常的对象，这一个过程称为捕获（catch）异常。

Java 异常强制用户考虑程序的强健性和安全性。异常处理不应用来控制程序的正常流程，其主要作用是捕获程序在运行时发生的异常并进行相应处理。编写代码处理某个方法可能出现的异常，可遵循如下三个原则：

1.  在当前方法声明中使用 try catch 语句捕获异常。
2.  一个方法被覆盖时，覆盖它的方法必须拋出相同的异常或异常的子类。
3.  如果父类抛出多个异常，则覆盖方法必须拋出那些异常的一个子集，而不能拋出新异常。

## 异常类型

在 Java 中所有异常类型都是内置类 java.lang.Throwable 类的子类，即 Throwable 位于异常类层次结构的顶层。Throwable 类下有两个异常分支 Exception 和 Error，如图 1 所示。

![](img/ab646e446e50b107245c8e9a2288832c.jpg)
图 1 异常结构图
由图 2 可以知道，Throwable 类是所有异常和错误的超类，下面有 Error 和 Exception 两个子类分别表示错误和异常。其中异常类 Exception 又分为运行时异常和非运行时异常，这两种异常有很大的区别，也称为不检查异常（Unchecked Exception）和检查异常（Checked Exception）。

*   Exception 类用于用户程序可能出现的异常情况，它也是用来创建自定义异常类型类的类。
*   Error 定义了在通常环境下不希望被程序捕获的异常。Error 类型的异常用于 Java 运行时由系统显示与运行时系统本身有关的错误。堆栈溢出是这种错误的一例。
*   本章不讨论关于 Error 类型的异常处理，因为它们通常是灾难性的致命错误，不是程序可以控制的。本章接下来的内容将讨论 Exception 类型的异常处理。

运行时异常都是 RuntimeException 类及其子类异常，如 NullPointerException、IndexOutOfBoundsException 等，这些异常是不检查异常，程序中可以选择捕获处理，也可以不处理。这些异常一般由程序逻辑错误引起，程序应该从逻辑角度尽可能避免这类异常的发生。

非运行时异常是指 RuntimeException 以外的异常，类型上都属于 Exception 类及其子类。从程序语法角度讲是必须进行处理的异常，如果不处理，程序就不能编译通过。如 IOException、ClassNotFoundException 等以及用户自定义的 Exception 异常，一般情况下不自定义检查异常。表 1 列出了一些常见的异常类型及它们的作用。

表 1 Java 中常见的异常类型

| 异常类型 | 说明 |
| Exception | 异常层次结构的根类 |
| RuntimeException | 运行时异常，多数 java.lang 异常的根类 |
| ArithmeticException | 算术谱误异常，如以零做除数 |
| ArraylndexOutOfBoundException | 数组大小小于或大于实际的数组大小 |
| NullPointerException | 尝试访问 null 对象成员，空指针异常 |
| ClassNotFoundException | 不能加载所需的类 |
| NumberF ormatException | 数字转化格式异常，比如字符串到 float 型数字的转换无效 |
| IOException | I/O 异常的根类 |
| F ileN otF oundException | 找不到文件 |
| EOFException | 文件结束 |
| InterruptedException | 线程中断 |
| IllegalArgumentException | 方法接收到非法参数 |
| ClassCastException | 类型转换异常 |
| SQLException | 操作数据库异常 |