# Java super 关键字：super 调用父类的构造方法、利用 super 访问父类成员

由于子类不能继承父类的构造方法，因此，要调用父类的构造方法，必须在子类的构造方法体的第一行使用 super() 方法。该方法会调用父类相应的构造方法来完成子类对象的初始化工作。

在以下情况下需要使用 super 关键字：

*   在类的构造方法中，通过 super 语句调用该类的父类的构造方法。
*   在子类中访问父类中的成员。

## 使用 super 调用父类的构造方法

子类可以通过 super 关键字来调用一个由父类定义的构造方法，格式如下：

```
super(parameter-list);
```

其中，parameter-list 指定了父类中构造方法所需的所有参数。super() 必须是在子类构造方法的主体第一行。

例如，在 Person 类中指定了两个构造方法。示例代码如下：

```
public People(String name,int age,String sex,String sn)
{
    this.name=name;
    this.age=age;
    this.sex=sex;
    this.sn=sn;
}
public People(String name,String sn)
{
    this.name=name;
    this.sn=sn;
}
```

那么，Student 类继承了 Person 类，就可以使用 super 语句来定义 Student 类的构造方法。示例代码如下：

```
public Student(String name,int age,String sex,String sn,String stuno,String department)
{
    super(name,age,sex,sn);    //调用父类中含有 4 个参数的构造方法
    this.stuNo=stuno;
    this.department=department;
}
public Student(String name,String sn,String stuNo)
{
    super(name,sn);    //调用父类中含有两个参数的构造方法
    this.stuNo=stuNo;
}
```

从上述 Student 类构造方法代码可以看出，super 用来直接调用父类中的构造方法，使用它可以使书写代码更简洁方便。

## 使用 super 访问父类成员

使用 super 访问父类中的成员与 this 关键字的使用相似，只不过它引用的是子类的父类，基本形式如下：

```
super.member
```

其中，member 是父类中的方法或属性名称。这种形式多用于子类的成员名隐藏了父类中的同名成员的情况。

#### 例 1

在 Animal 类和 Cat 类中分别定义了 public 类型的 name 属性和 private 类型的 name 属性，并且 Cat 类继承 Animal 类。那么，我们可以在 Cat 类中通过 super 关键字来访问父类 Animal 中的 name 属性，通过 this 关键字来访问本类中的 name 属性，如下面的代码：

```
//父类 Animal 的定义
public class Animal
{
    public String name;    //动物名字
}
//子类 Cat 的定义
public class Cat extends Animal
{
    private String name;    //名字
    public Cat(String aname,String dname)
    {
        super.name=aname;    //通过 super 关键字来访问父类中的 name 属性
        this.name=dname;    //通过 this 关键字来访问本类中的 name 属性
    }
    public String toString()
    {
        return"我是"+super.name+"，我的名字叫"+this.name;
    }
    public static void main(String[] args)
    {
        Animal cat=new Cat("动物","喵星人");
        System.out.println(cat);
    }
}
```

上述代码演示了使用 super 实现子类的成员名隐藏父类中同名成员的情况。尽管 Cat 类中的属性 name 隐藏了 Animal 类中的 name 属性，但是 super 允许访问父类中的 name 属性。另外，super 还可以用于调用被子类隐藏的方法。

运行程序，输出结果如下：

```
我是动物，我的名字叫喵星人
```