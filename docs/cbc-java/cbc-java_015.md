# Java 变量的作用域

变量的作用域规定了变量所能使用的范围，只有在作用域范围内变量才能被使用。根据变量声明地点的不同，变量的作用域也不同。

根据作用域的不同，一般将变量分为不同的类型：类变量、局部变量、方法参数变量及异常处理参数变量。下面对这几种变量进行详细说明。

## 类变量

类变量也称为成员变量，声明在类中，不属于任何一个方法，作用域是整个类。

#### 例 1

假设在一个类中声明了 3 个变量，下面编写一个测试类输出引起变量的值改变的示例代码。

变量声明代码如下所示：

```
public class DataClass
{
    int price=100;    //定义类变量 price
    price String name;    //定义类变量 name
    name int num;    //定义类变量 num
}
```

测试类代码如下所示：

```
public class Test
{
    public static void main(String[] args)
    {
        DataClass dc=new DataCLass();
        System.out.println("name="+dc.name);
        System.out.println("num="+dc.num);
        System.out.println("price="+dc.price);
    }
}
```

运行效果如下所示：

```
name=null
num=0
price=100
```

在本例的第一段代码中定义了 3 个成员变量，并对其中第一个变量 price 进行了初始化，而第二个 name 变量和第三个变量 num 没有进行初始化。由输出结果可以看出，第一个变量的值为显示初始化的值，第二个和第三个变量的值则为系统默认初始化的值。

## 局部变量

局部变量是指在方法或者方法代码块中定义的变量，其作用域是其所在的代码块。

#### 例 2

声明两个局部变量并输出其值，其实现代码如下：

```
public class Test2
{
    public static void main(String[] args)
    {
        int a=7;
        if(5>3)
        {
            int s=3;    //声明一个 int 类型的局部变量
            System.out.println("s="+s);
            System.out.println("a="+a);
        }
        System.out.println("a="+a);
    }
}
```

上述实例中定义了 a 和 s 两个局部变星，其中 int 类型的 a 的作用域是整个 main() 方法，而 int 类型的变量 s 的作用域是 if 语句的代码块内，其执行结果如下：

```
s=3
a=7
a=7
```

## 方法参数变量

作为方法参数声明的变量的作用域是整个方法。

#### 例 3

声明一个方法参数变量，实现代码如下：

```
public class Test3
{
    public static void testFun(int n)
    {
        System.out.println("n="+n);
    }
    public static void main(String[] args)
    {
        testFun(B);
    }
}
```

在上述实例中定义了一个 testFun() 方法，该方法中包含一个 int 类型的参数变量 n，其作用域是 testFun() 方法体内。当调用方法时传递进了一个参数 3，因此其输出控制台的 n 值是 3。

## 异常处理参数变量

异常处理参数变量的作用域是在异常处理块中，该变量是将异常处理参数传递给异常处理块，与方法参数变量类似。

#### 例 4

声明一个异常处理语句，实现代码如下：

```
public class Test4
{
    public static void test()
    {
        try
        {
            System.out.println("Hello!Exception!");
        }
        catch(Exception e)
        {   //异常处理块，参数为 Exception 类型
            e.printStackTrace();
        }
    }
    public static void main(String[] args)
    {
        test();
    }
}
```

在上述实例中定义了异常处理语句，异常处理块 catch 的参数为 Exception 类型的变量 e，作用域是整个 catch 块。