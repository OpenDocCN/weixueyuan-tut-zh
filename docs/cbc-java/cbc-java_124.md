# Java 方法重写

在子类中如果创建了一个与父类中相同名称、相同返回值类型、相同参数列表的方法，只是方法体中的实现不同，以实现不同于父类的功能，这种方式被称为方法重写，又称为方法覆盖。

在重写方法时，需要遵循下面的规则：

*   参数列表必须完全与被重写的方法参数列表相同，否则不能称其为重写。
*   返回的类型必须与被重写的方法的返回类型相同，否则不能称其为重写。
*   访问修饰符的限制一定要大于被重写方法的访问修饰符（public>protected>default>private）。
*   重写方法一定不能抛出新的检査异常或者比被重写方法声明更加宽泛的检査型异常。例女口，父类的一个方法声明了一个检査异常 IOException，在重写这个方法时就不能抛出 Exception，只能拋出 IOException 的子类异常，可以抛出非检査异常。

#### 例 1

每种动物都有名字和年龄属性，但是喜欢吃的食物是不同的，比如狗喜欢吃骨头、猫喜欢吃鱼等，因此每种动物的介绍方式是不一样的。

下面编写 Java 程序，在父类 Animal 中定义 getInfo() 方法，并在子类 Cat 中重写该方法， 实现猫的介绍方式。父类 Animal 的代码如下：

```
public class Animal
{
    public String name;    //名字
    public int age;    //年龄
    public Animal(String name,int age)
    {
        this.name=name;
        this.age=age;
    }
    public String getInfo()
    {
        return"我叫"+name+"，今年"+age+"岁了。";
    }
}
```

子类 Cat 的代码如下：

```
public class Cat extends Animal
{
    private String hobby;
    public Cat(String name,int age,String hobby)
    {
        super(name,age);
        this.hobby=hobby;
    }
    public String getInfo()
    {
        return"喵！大家好！我叫"+this.name+"，我今年"+this.age+"岁了，我爱吃"+hobby+"。";
    }
    public static void main(String[] args)
    {
        Animal animal=new Cat("小白",2,"鱼");
        System.out.println(animal.getInfo());
    }
}
```

如上述代码，在 Animal 类中定义了一个返回值类型为 String、名称为 getInfo() 的方法，而 Cat 类继承自该类，因此 Cat 类同样含有与 Animal 类中相同的 getInfo() 方法。但是我们在 Cat 类中又重新定义了一个 getInfo() 方法，即重写了父类中的 getInfo() 方法。

在 main() 方法中，创建了 Cat 类的对象 animal，并调用了 getInfo() 方法。输出的结果如下：

```
喵！大家好！我叫小白，我今年 2 岁了，我爱吃鱼。
```

如果子类中创建了一个成员变量，而该变量的类型和名称都与父类中的同名成员变量相同，我们则称作变量隐藏。