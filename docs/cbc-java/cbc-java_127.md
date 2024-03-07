# Java 接口：接口的定义和接口的实现，定义接口，实现接口

接口类似于类，但接口的成员没有执行体，它只是方法、属性、事件和索引符的组合而已。接口不能被实例化，接口没有构造方法，没有字段。在应用程序中，接口就是一种规范，它封装了可以被多个类继承的公共部分。

## 定义接口

接口继承和实现继承的规则不同，一个类只有一个直接父类，但可以实现多个接口。Java 接口本身没有任何实现，只描述 public 行为，因此 Java 接口比 Java 抽象类更抽象化。Java 接口的方法只能是抽象的和公开的，Java 接口不能有构造方法，Java 接口可以有 public、Static 和 final 属性。

接口把方法的特征和方法的实现分隔开来，这种分隔体现在接口常常代表一个角色，它包装与该角色相关的操作和属性，而实现这个接口的类便是扮演这个角色的演员。一个角色由不同的演员来演，而不同的演员之间除了扮演一个共同的角色之外，并不要求其他的共同之处。

接口对于其声明、变量和方法都做了许多限制，这些限制作为接口的特征归纳如下：

*   具有 public 访问控制符的接口，允许任何类使用；没有指定 public 的接口，其访问将局限于所属的包。
*   方法的声明不需要其他修饰符，在接口中声明的方法，将隐式地声明为公有的（public）和抽象的（abstract）。
*   在 Java 接口中声明的变量其实都是常量，接口中的变量声明，将隐式地声明为 public、static 和 final，即常量，所以接口中定义的变量必须初始化。
*   接口没有构造方法，不能被实例化。例如：

```
public interface A
{
    publicA(){…}    //编译出错，接口不允许定义构造方法
}
```

*   一个接口不能够实现另一个接口，但它可以继承多个其他接口。子接口可以对父接口的方法和常量进行重写。例如：

```
public interface Student!nterface extends PeopleInterface
{
    //接口 StudentInterface 继承 PeopleInterface
    int age=25;    //常量 age 重写父接口中的 age 常量
    void getInfo();    //方法 getInfo()重写父接口中的 getInfo()方法
}
```

Java 接口的定义方式与类基本相同，不过接口定义使用的关键字是 interface，接口定义由接口声明和接口体两部分组成。语法格式如下：

```
[public] interface interface_name [extends interface1_name[, interface2_name,…]]
{
    //接口体，其中可以包含定义常量和声明方法
    [public] [static] [final] type constant_name=value;    //定义常量
    [public] [abstract] returnType method_name(parameter_list);    //声明方法
}
```

其中，public 表示接口的修饰符，当没有修饰符时，则使用默认的修饰符，此时该接口的访问权限仅局限于所属的包；interfaCe_name 表示接口的名称，可以是任何有效的标识符；extends 表示接口的继承关系；interface1_name 表示要继承的接口名称；constant_name 表示变量名称，一般是 static 和 final 型的；returnType 表示方法的返回值类型；parameter_list 表示参数列表，在接口中的方法是没有方法体的。

提示：如果接口本身被定义为 public，则所有的方法和常量都是 public 型的。

例如，定义一个接口 MyInterface，并在该接口中声明常量和方法，如下：

```
public interface Mylnterface
{    //接口 myInterface
    String name;    //不合法，变量 name 必须初始化
    int age=20;    //合法，等同于 public static final int age=20;
    void getInfo();    //方法声明，等同于 public abstract void getInfo();
}
```

## 实现接口

接口被定义后，一个或者多个类都可以实现该接口，这需要在实现接口的类的定义中包含 implements 子句，然后实现由接口定义的方法。实现接口的一般形式如下：

```
<public> class <class_name> [extends superclass_name] [implements interface[, interface…]]
{
    //主体
}
```

如果一个类实现多个接口，这些接口需要使用逗号分隔。如果一个类实现两个声明了同样方法的接口，那么相同的方法将被其中任一个接口使用。实现接口的方法必须声明为 public，而且实现方法的类型必须严格与接口定义中指定的类型相匹配。

#### 例 1

在程序的开发中，需要完成两个数的求和运算和比较运算功能的类非常多。那么可以定义一个接口来将类似功能组织在一起。下面创建一个示例，具体介绍接口的实现方式。

(1) 创建一个名称为 IMath 的接口，代码如下：

```
public interface IMath
{
    public int sum();    //完成两个数的相加
    public int maxNum(int a,int b);    //获取较大的数
}
```

(2) 定义一个 MathClass 类并实现 IMath 接口，MathClass 类实现代码如下：

```
public class MathClass implements IMath
{
    private int num1;    //第 1 个操作数
    private int num2;    //第 2 个操作数
    public MathClass(int num1,int num2)
    {
        //构造方法
        this.num1=num1;
        this.num2=num2;
    }
    //实现接口中的求和方法
    public int sum()
    {
        return num1+num2;
    }
    //实现接口中的获取较大数的方法
    public int maxNum(int a,int b)
    {
        if(a>=b)
        {
            return a;
        }
        else
        {
            return b;
        }
    }
}
```

在实现类中，所有的方法都使用了 public 访问修饰符声明。无论何时实现一个由接口定义的方法，它都必须实现为 public，因为接口中的所有成员都显式声明为 public。

(3) 最后创建测试类 NumTest，实例化接口的实现类 MathClass，调用该类中的方法并输出结果。该类内容如下：

```
public class NumTest
{
    public static void main(String[] args)
    {
        //创建实现类的对象
        MathClass calc=new MathClass(100, 300);
        System.out.println("100 和 300 相加结果是："+calc.sum());
        System.out.println("100 比较 300，哪个大："+calc.maxNum(100, 300));
    }
}
```

程序运行结果如下所示。

```
100 和 300 相加结果是：400
100 比较 300，哪个大：300
```

在该程序中，首先定义了一个 IMath 的接口，在该接口中只声明了两个未实现的方法，这两个方法需要在接口的实现类中实现。在实现类 MathClass 中定义了两个私有的属性，并赋予两个属性初始值，同时创建了该类的构造方法。因为该类实现了 MathClass 接口，因此必须实现接口中的方法。在最后的测试类中，需要创建实现类对象，然后通过实现类对象调用实现类中的方法。