# Java 类的定义及定义类时可用的关键字

类是 Java 中的一种重要的复合数据类型，也是组成 Java 程序的基本要素，因为所有的 Java 程序都是基于类的。本节介绍如何定义类。

在 Java 中定义一个类，需要使用 class 关键字、一个自定义的类名和一对表示程序体的大括号。完整语法如下：

```
[public][abstract|final]class<class_name>[extends<class_name>]
[implements<interface_name>]
{
    //定义属性部分
    <property_type><property1>;
    <property_type><property2>;
    <property_type><property3>;
    …
    //定义方法部分
    function1();
    function2();
    function3();
    …
}
```

上述语法中各关键字的描述如下。

*   `public`：表示“共有”的意思。如果使用 public 修饰，则可以被其他类和程序访问。每个 Java 程序的主类都必须是 public 类，作为公共工具供其他类和程序使用的类应定义为 public 类。
*   `abstract`：如果类被 abstract 修饰，则该类为抽象类，抽象类不能被实例化，但抽象类中可以有抽象方法（使用 abstract 修饰的方法）和具体方法（没有使用 abstract 修饰的方法）。继承该抽象类的所有子类都必须实现该抽象 类中的所有抽象方法（除非子类也是 抽象类）。
*   `final`：如果类被 final 修饰，则不允许被继承。
*   `class`：声明类的关键字。
*   `class_name`：类的名称。
*   `extends`：表示继承其他类。
*   `implements`：表示实现某些接口。
*   `property_type`：表示成员变量的类型。
*   `property`：表示成员变量名称。
*   `function()`：表示成员方法名称。

类名应该以下划线（_）或字母开头，最好以字母开头；第一个字母最好大写，如果类名由多个单词组成，则每个单词的首字母最好都大写；类名不能为 Java 中的关键字，例如 boolean、this、int 等；类名不能包含任何嵌入的空格或点号以及除了下划线（_）和美元符号（$）字符之外的特殊字符。

#### 例 1

创建一个新的类，就是创建一个新的数据类型。实例化一个类，就是得到类的一个对象。因此，对象就是一组变量和相关方法的集合，其中变量表明对象的状态和属性，方法表明对象所具有的行为。定义一个类的步骤如下所述。

(1) 声明类。编写类的最外层框架，声明一个名称为 Person 的类。

```
public class Person
{
    //类的主体
}
```

(2) 编写类的属性。类中的数据和方法统称为类成员。其中，类的属性就是类的数据成员。通过在类的主体中定义变量来描述类所具有的特征（属性），这里声明的变量称为类的成员变量。

(3) 编写类的方法。类的方法描述了类所具有的行为，是类的方法成员。可以简单地把方法理解为独立完成某个功能的单元模块。

下面来定义一个简单的 Person 类。

```
public class Person
{
    private String name;    // 姓名
    private int age;    // 年龄
    public void teli()
    {   //定义说话的方法
        System.out.println(name+"今年"+age+"岁！");
    }
}
```

如上述代码，在 Person 类中首先定义了两个属性，分别为 name 和 age，然后定义了一个名称为 tell() 的方法。