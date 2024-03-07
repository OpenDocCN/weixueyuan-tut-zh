# Java 方法重载

方法重载是 Java 多态性的表现。在 Java 语言中，同一个类中的两个或多个方法可以共享同一个名称，只要它们的参数声明不同即可，这种情况被称为方法重载。方法重载有两种情况：普通方法的重载和构造方法的重载。

例如，在 JDK 的 java.io.PrintStream 中定义了十多个同名的 println() 方法。

```
public void printin(int i){…}
public void println(double d){…}
public void println(String s){…}
```

这些方法完成的功能类似，都是格式化输出。根据参数的不同来区分它们，以进行不同的格式化处理和输出。它们之间就构成了方法的重载。实际调用时，根据实参的类型来决定调用哪一个方法。例如：

```
System.out.println(102);    //调用 println(int i)方法
System.out.println(102.25);    //调用 println(double d)方法
System.out.println("价格为 102.25");    //调用 println(String s)方法
```

方法重载时必须要注意以下两点：

*   重载方法的参数列表必须和被重载的方法不同，并且这种不同必须足以清楚地确定要调用哪一个方法。
*   重载方法的返回值类型可以和被重载的方法相同，也可以不同，但是只有返回值类型不同不能表示为重载。

#### 例 1

在比较数值时，数值的个数和类型是不固定的，可能是两个 int 类型的数值，也可能是两个 double 类型的数值，或者是两个 double、一个 int 类型的数值；在这种情况下就可以使用方法的重载来实现数值之间的比较功能。具体实现代码如下：

```
public class Overloading
{
    public void max(int a,int b)
    {
        //含有两个 int 类型参数的方法
        System.out.println(a>b?a:b);
    }
    public void max(double a,double b)
    {
        //含有两个 double 类型参数的方法
        System.out.println(a>b?a:b);
    }
    public void max(double a,double b,int c)
    {
        //含有两个 double 类型参数和一个 int 类型参数的方法
        double max=(double)(a>b?a:b);
        System.out.println(c>max?c:max);
        public static void main(String[] args)
        {
            OverLoading ol=new OverLoading();
            System.out.println("1 与 5 比较，较大的是：");
            ol.max(1,5);
            System.out.println("5.205 与 5.8 比较，较大的是：");
            ol.max(5.205, 5.8);
            System.out.println("2.15、0.05、58 中，较大的是：");
            ol.max(2.15,0.05,58);
        }
    }
}
```

如上述代码，在 OverLoading 类中定义了 3 个相同名称的方法，其参数列表是不相同的。当运行时，Java 虚拟机会根据传递过来的不同参数来调用不同的方法。运行结果如下：

```
1 与 5 比较，较大的是：
5
5.205 与 5.8 比较，较大的是：
5.8
2.15、0.05、58 中，较大的是：
58.0
```