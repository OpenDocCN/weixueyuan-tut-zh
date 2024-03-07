# Java main()方法

main() 方法定义的语法如下所示。

```
public static void main(String[] args)
{
    //方法体
}
```

其中，使用 main() 方法时应该注意如下几点：

*   访问控制权限是公有的（public）。
*   main() 方法是静态的。如果要在 main() 方法中调用本类中的其他方法，则该方法也必须是静态的，否则需要先创建本类的实例对象，然后再通过对象调用成员方法。
*   main() 方法没有返回值，只能使用 void。
*   main() 方法具有一个字符串数组参数，用来接收执行 Java 程序的命令行参数。命令行参数作为字符串，按照顺序依次对应字符串数组中的元素。
*   除了形参变量名可以任意设置以外，main() 方法中的其他内容都是固定不变的。

下面的示例代码演示了如何在 main() 方法中调用本类的静态和非静态方法。

```
public class Student
{
    public void Speak1()
    {
        System.out.println("你好!");
    }
    public static void Speak2()
    {
        System.out.println("Java!");
    }
    public static void main(String[] args)
    {
        //Speak1();    //错误调用
        Speak2();    //可以直接调用静态方法 Speak2()
        Student t=new Student();
        t.Speak1();    //调用非静态方法，需要通过类的对象来调用
    }
}
```

#### 例 1

创建一个 Java 程序，编写代码实现程序执行时统计传递参数的数量及每个参数值。示例代码如下：

```
public class TestMain
{
    //@param args
    public static void main(String[] args)
    {
        //TODO Auto-generated method stub
        int n=args.length;    //获取参数数量
        System.out.println("一共有 "+n+" 个参数");
        if(n>0)
        {   
            //判断参数个数是否大于 0
            for(int i=0;i<n;i++)
            {
                System.out.println(args[i]);
            }
        }
    }
}
```

(1) 将代码保存到 TestMain.java 文件中，然后用如下 Java 命令对程序进行编译：

```
javac TestMain.java
```

(2) 程序编译成功后用如下 Java 命令执行 Test 程序：

```
java TestMain 参数列表    //多个之间用空格隔开
```

这里使用如下三个语句执行程序：

```
java TestMain
java TestMain apple banana
java TestMain one two three four five six
```

(3) 执行结果如下所示：

```
D:\2HHT\ShuGao\ch08\bin>java TestMain
一共有 0 个参数
D:\2HHT\ShuGao\ch08\bin>java TestMain apple banana
一共有 2 个参数
apple
banana
D:\ZHHT\ShuGao\ch08\bin>java TestMain one two three four five six
一共有 6 个参数 one
three
four
five
six
D:\ZHHT\ShuGao\ch08\bin>
```

由此可见，main() 方法可以以字符串的形式接收命令行参数，然后在方法体内进行处理。