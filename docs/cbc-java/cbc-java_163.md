# Java 系统流

每个 Java 程序运行时都带有一个系统流，系统流对应的类为 java.lang.System。Sytem 类封装了 Java 程序运行时的 3 个系统流，分别通过 in、out 和 err 变量来引用。这些变量的作用域为 public 和 static，因此在程序的任何部分都不需引用 System 对象就可以使用它们。

*   System.in：标准输入流，默认设备是键盘。
*   System.out：标准输出流，默认设畜是控制台。
*   System.err：标准错误流，默认设备是控制台。

#### 例 1

下面的程序演示了如何使用 System.in 读取字节数组，使用 System.out 输出字节数组。

```
package ch13;
import java.io.IOException;
public class Test01
{
    public static void main(String[] args)
    {
        byte[] byteData=new byte[100];    //声明一个字节数组
        System.out.println("请输入英文：");
        try
        {
            System.in.read(byteData);
        }
        catch (IOException e)
        {
            e.printStackTrace();
        }
        System.out.println("您输入的内容如下：");
        for(int i=0;i<byteData.length;i++)
        {
            System.out.print((char)byteData[i]);
        }
    }
```

}

该程序的运行结果如下所示：

```
请输入英文：
abcdefg hijklmn opqrst uvwxyz
您输入的内容如下：
abcdefg hijklmn opqrst uvwxyz
```

System.in 是 InputStream 类的一个对象，因此上述代码的 System.in.read() 方法实际是访问 InputStream 类定义的 read() 方法。该方法可以从键盘读取一个或多个字符。对于 System.out 输出流主要用于将指定内容输出到控制台。

System.out 和 System.error 是 PrintStream 类的对象。因为 PrintStream 是一个从 OutputStream 派生的输出流，所以它还执行低级别的 write() 方法。因此，除了 print() 和 println() 方法可以完成控制台输出以外，System.out 还可以调用 write() 方法实现控制台输出。

write() 的简单形式如下：

```
void write(int byteval) throws 10Exception
```

该方法通过 byteval 向文件写入指定的字节。在实际操作中，print() 方法和 println() 方法比 write() 方法更常用。

注意：尽管它们通常用于对控制台进行读取和写入字符，但是这些都是字节流。因为预定义流是没有引入字符流的 Java 原始规范的一部分，所以它们不是字符流而是字节流，但是在 Java 中可以将它们打包到基于字符的流中使用。