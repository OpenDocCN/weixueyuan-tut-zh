# Java 类的属性：成员变量的定义和声明

在 Java 中类的成员变量定义了类的属性。例如，一个学生类中一般需要有姓名、性别和年龄等属性，这时就需要定义姓名、性别和年龄 3 个属性。声明成员变量的语法如下：

```
[public|protected|private][static][final]<type><variable_name>
```

各参数的含义如下。

*   public、protected、private：用于表示成员变量的访问权限。
*   static：表示该成员变量为类变量，也称为静态变量。
*   final：表示将该成员变量声明为常量，其值无法更改。
*   type：表示变量的类型。
*   variable_name：表示变量名称。

可以在声明成员变量的同时对其进行初始化，如果声明成员变量时没有对其初始化，则系统会使用默认值初始化成员变量。

初始化的默认值如下：

*   整数型（byte、short、int 和 long）的基本类型变量的默认值为 0。
*   单精度浮点型（float）的基本类型变量的默认值为 0.0f。
*   双精度浮点型（double）的基本类型变量的默认值为 0.0d。
*   字符型（char）的基本类型变量的默认值为 “\u0000”。
*   布尔型的基本类型变量的默认值为 false。
*   数组引用类型的变量的默认值为 null。如果创建了数组变量的实例，但没有显式地为每个元素赋值，则数组中的元素初始化值采用数组数据类型对应的默认值。

定义类的成员变量的示例如下：

```
public class Student
{
    public String name;    //姓名
    final int sex=0;    //性别：0 表示女孩，1 表示男孩
    private int age;    //年龄
}
```

上述示例的 Student 类中定义了 3 个成员变量：String 类型的 name、int 类型的 sex 和 int 类型的 age。其中，name 的访问修饰符为 public，初始化值为 null；sex 的访问修饰符为 friendly（默认），初始化值为 0，表示性别为女，且其值无法更改；age 的访问修饰符为 private，初始化值为 0。

#### 例 1

下面以一个简单的例子来介绍成员变量的初始值，代码如下所示。

```
public class Counter
{
    static int sum;
    public static void main(String[] args)
    {
        System.out.println(sum);
    }
}
```

在这里用静态的方法来修饰变量 sum，输出结果是 int 类型的初始值，即：0。