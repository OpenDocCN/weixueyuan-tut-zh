# Java 构造方法

构造方法是类的一种特殊方法，用来初始化类的一个新的对象。Java 中的每个类都有一个默认的构造方法，它必须具有和类名相同的名称，而且没有返回类型。构造方法的默认返回类型就是对象类型本身，并且构造方法不能被 static、final、synchronized、abstract 和 native 修饰。

提示：构造方法用于初始化一个新对象，所以用 static 修饰没有意义；构造方法不能被子类继承，所以用 final 和 abstract 修饰没有意义；多个线程不会同时创建内存地址相同的同一个对象，所以用 synchronized 修饰没有必要。

构造方法的语法格式如下：

```
class class_name
{
    public class_name(){}    //默认无参构造方法
    public ciass_name([paramList]){}    //定义构造方法
    …
    //类主体
}
```

在一个类中，与类名相同的方法就是构造方法。每个类可以具有多个构造方法，但要求它们各自包含不同的方法参数。

#### 例 1

构造方法主要有无参构造方法和有参构造方法两种，示例如下：

```
public class MyClass
{
    private int m;    //定义私有变量
    MyClass()
    {
        //定义无参的构造方法
        m=0;
    }
    MyCiass(int m)
    {
        //定义有参的构造方法
        this.m=m;
    }
}
```

该示例定义了两个构造方法，分别是无参构造方法和有参构造方法。在一个类中定义多个具有不同参数的同名方法，这就是方法的重载。这两个构造方法的名称都与类名相同，均为 MyClass。在实例化该类时可以调用不同的构造方法进行初始化。

注意：类的构造方法不是要求必须定义的。如果在类中没有定义任何一个构造方法，则 Java 会自动为该类生成一个默认的构造方法。默认的构造方法不包含任何参数，并且方法体为空。如果类中显式地定义了一个或多个构造方法，则 Java 不再提供默认构造方法。

#### 例 2

要在不同的条件下使用不同的初始化行为创建类的对象，这时候就需要在一个类中创建多个构造方法。下面通过一个示例来演示构造方法的使用。

(1) 首先在员工类 Worker 中定义两个构造方法，代码如下：

```
public class Worker
{
    public String name;    //姓名
    private int age;    //年龄
    //定义带有一个参数的构造方法
    public Worker(String name)
    {
        this.name=name;
    }
    //定义带有两个参数的构造方法
    public Worker(String name,int age)
    {
        this.name=name;
        this.age=age;
    }
    public String toString()
    {
        return"大家好！我是新来的员工，我叫"+name+"，今年"+age+"岁。";
    }
}
```

在 Worker 类中定义了两个属性，其中 name 属性不可改变。分别定义了带有一个参数和带有两个参数的构造方法，并对其属性进行初始化。最后定义了该类的 toString() 方法，返回一条新进员工的介绍语句。

提示：Object 类具有一个 toString() 方法，该方法是个特殊的方法，创建的每个类都会继承该方法，它返回一个 String 类型的字符串。如果一个类中定义了该方法，则在调用该类对象时，将会自动调用该类对象的 toString() 方法返回一个字符串，然后使用“System.out.println(对象名)”就可以将返回的字符串内容打印出来。

(2) 在 TestWorker 类中创建 main() 方法作为程序的入口处，在 main() 方法中调用不同的构造方法实例化 Worker 对象，并对该对象中的属性进行初始化，代码如下：

```
public class TestWorker
{
    public static void main(String[] args)
    {
        System.out.println("-----------带有一个参数的构造方法-----------");
        //调用带有一个参数的构造方法，Staff 类中的 sex 和 age 属性值不变
        Worker worker1=new Worker("张强");
        System.out.println(worker1);
        System.out.println("-----------带有两个参数的构造方法------------");
        //调用带有两个参数的构造方法，Staff 类中的 sex 属性值不变
        Worker worker2=new Worker("李丽",25);
        System.out.println(worker2);
    }
}
```

在上述代码中，创建了两个不同的 Worker 对象：一个是姓名为张强的员工对象，一个是姓名为李丽、年龄为 25 的员工对象。对于第一个 Worker 对象 Worker1，并未指定 age 属性值，因此程序会将其值采用默认值 0。对于第二个 Worker 对象 Worker2，分别对其指定了 name 属性值和 age 属性值，因此程序会将传递的参数值重新赋值给 Worker 类中的属性值。

运行 TestWorker 类，输出的结果如下：

```
-----------带有一个参数的构造方法-----------
大家好！我是新来的员工，我叫张强，今年 0 岁。
-----------带有两个参数的构造方法------------
大家好！我是新来的员工，我叫李丽，今年 25 岁。
```

通过调用带参数的构造方法，在创建对象时，一并完成了对象成员的初始化工作，简化了对象初始化的代码。