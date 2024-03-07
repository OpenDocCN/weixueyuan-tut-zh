# Java System 类详解

System 类代表系统，系统级的很多属性和控制方法都放置在该类的内部。该类位于 java. lang 包。由于该类的构造方法是 private 的，所以无法创建该类的对象，也就是无法实例化该类。

System 类内部的成员变量和成员方法都是 static 的，所以可以方便地进行调用。

## System 类的成员变量

System 类有 3 个静态成员变量，分别是 PrintStream out、InputStream in 和 PrintStream err。

#### 1\. PrintStream out

标准输出流。此流已打开并准备接收输出数据。通常，此流对应于显示器输出或者由主机环境或用户指定的另一个输出目标。

例如，编写一行输出数据的典型方式是：

```
System.out.println(data);
```

其中，println 方法是属于流类 PrintStream 的方法，而不是 System 中的方法。

#### 2\. InputStream in

标准输入流。此流已打开并准备提供输入数据。通常，此流对应于键盘输入或者由主机环境或用户指定的另一个输入源。

#### 3\. PrintStream err

标准的错误输出流。其语法与 System.out 类似，不需要提供参数就可输出错误信息。也可以用来输出用户指定的其他信息，包括变量的值。

#### 例 1

编写一个 Java 程序，使用本节介绍的 System 类实现从键盘输入字符并显示出来。 具体实现代码如下：

```
import java.io.IOException;
public class Test06
{
    public static void main(String[] args)
    {
        System.out.println("请输入字符，按回车键结束输入:");
        int c;
        try
        {
            c=System.in.read();    //读取输入的字符
            while(c!='\r')
            {    //判断输入的字符是不是回车
                System.out.print((char) c);    //输出字符
                c=System.in.read();
            }
        }
        catch(IOException e)
        {
            System.out.println(e.toString());
        }
        finally
        {
            System.err.println();
        }
    }
}
```

以上代码中，System.in.read() 语句读入一个字符，read() 方法是 InputStream 类拥有的方法。变量 c 必须用 int 类型而不能用 char 类型，否则会因为丢失精度而导致编译失败。

以上的程序如果输入汉字将不能正常输出。如果要正常输出汉字，需要把 System.in 声明为 InputStreamReader 类型的实例，最终在 try 语句块中的代码为：

```
InputStreamReader in=new InputStreamReader(System.in,"GB2312");
c=in.read();
while(c!='\r')
{
    System.out.print((char) c);
    c=in.read();
}
```

如上述代码所示，语句 InputStreamReader in=new InputStreamReader(System.in,"GB2312") 声明一个新对象 in，它从 Reader 继承而来，此时就可以读入完整的 Unicode 码，显示正常的汉字。

## System 类的成员方法

System 类中提供了一些系统级的操作方法，常用的方法有 arraycopy()、currentTimeMillis()、exit()、gc() 和 getProperty()。

#### 1\. arraycopy() 方法

该方法的作用是数组复制，即从指定源数组中复制一个数组，复制从指定的位置开始，到目标数组的指定位置结束。该方法的具体定义如下：   

```
public static void arraycopy(Object src,int srcPos,Object dest,int destPos,int length)
```

其中，src 表示源数组，srcPos 表示从源数组中复制的起始位置，dest 表示目标数组，destPos 表示要复制到的目标数组的起始位置，length 表示复制的个数。

#### 例 2

下面的示例代码演示了 arraycopy() 方法的使用：

```
public class System_arrayCopy
{
    public static void main(String[] args)
    {
        char[] srcArray={'A','B','C','D'};
        char[] destArray={'E','F','G','H'};
        System.arraycopy(srcArray,1,destArray,1,2);
        System.out.println("源数组：");
        for(int i=0;i<srcArray.length;i++)
        {
            System.out.println(srcArray[i]);
        }
        System.out.println("目标数组：");
        for(int j=0;j<destArray.length;j++)
        {
            System.out.println(destArray[j]);
        }
    }
}
```

如上述代码，将数组 srcArray 中，从下标 1 开始的数据复制到数组 destArray 从下标 1 开始的位置，总共复制两个。也就是将 srcArray[1] 复制给 destArray[1]，将 srcArray[2] 复制给 destArray[2]。这样经过复制之后，数组 srcArray 中的元素不发生变化，而数组 destArray 中的元素将变为 E、B、C、 H，下面为输出结果：

```
源数组：
A
B
C
D
目标数组：
E
B
C
H
```

#### 2\. currentTimeMillis() 方法

该方法的作用是返回当前的计算机时间，时间的格式为当前计算机时间与 GMT 时间（格林尼治时间）1970 年 1 月 1 日 0 时 0 分 0 秒所差的毫秒数，例如：

```
long m=System.currentTimeMillis();
```

上述语句将获得一个长整型的数字，该数字就是以差值表达的当前时间。

#### 例 3

使用 currentTimeMillis() 方法来显示时间不够直观，但是可以很方便地进行时间计算。例如，计算程序运行需要的时间就可以使用如下的代码：

```
public class System_currentTimeMillis
{
    public static void main(String[] args)
    {
        long start=System.currentTimeMillis();
        for(int i=0;i<100000000;i++)
        {
            int temp=0;
        }
        long end=System.currentTimeMillis();
        long time=end-start;
        System.out.println("程序执行时间"+time+"秒");
    }
}
```

上述代码中的变量 time 的值表示代码中 for 循环执行所需要的毫秒数，使用这种方法可以测试不同算法的程序的执行效率高低，也可以用于后期线程控制时的精确延时实现。

#### 3\. exit() 方法

该方法的作用是终止当前正在运行的 Java 虚拟机，具体的定义格式如下：

```
public static void exit(int status)
```

其中，status 的值为 0 时表示正常退出，非零时表示异常退出。使用该方法可以在图形界面编程中实现程序的退出功能等。

#### 4\. gc() 方法

该方法的作用是请求系统进行垃圾回收。至于系统是否立刻回收，取决于系统中垃圾回收算法的实现以及系统执行时的情况。定义如下：

```
public static void gc()
```

#### 5\. getProperty() 方法

该方法的作用是获得系统中属性名为 key 的属性对应的值，具体的定义如下：

```
public static String getProperty(String key)
```

系统中常见的属性名以及属性的说明如表 1 所示。

表 1 系统常见属性

| 属性名 | 属性说明 |
| java. version | Java 运行时琢境版本 |
| java.home | Java 安装目录 |
| os.name | 操作系统的名称 |
| os.version | 操作系统的版本 |
| user.name | 用户的账户名称 |
| user.home | 用户的主目录 |
| user, dir | 用户的当前工作目录 |

#### 例 4

下面的示例演示了 getProperty() 方法的使用。

```
public class System_getProperty
{
    public static void main(String[] args)
    {
        String jversion=System.getProperty("java.version");
        String oName=System.getProperty("os.name");
        String user=System.getProperty("user.name");
        System.out.println("Java 运行时环境版本："+jversion);
        System.out.println("当前操作系统是："+oName);
        System.out.println("当前用户是："+user);
    }
}
```

运行该程序，输出的结果如下：

```
Java 运行时环境版本：1.6.0_26
当前操作系统是：Windows 7
当前用户是：Administrator
```

提示：使用 getProperty() 方法可以获得很多系统级的参数以及对应的值，这里不再一一举例。