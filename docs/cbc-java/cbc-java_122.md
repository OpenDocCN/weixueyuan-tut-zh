# Java 对象类型转换和强制对象类型转换

Java 语言允许某个类型的引用变量引用子类的实例，而且可以对这个引用变量进行类型转换。如果把引用类型转换为子类类型，则称为向下转型；如果把引用类型转换为父类类型，则称为向上转型。

例如，Creature 类表示生物类，Animal 类表示动物类，该类对应的子类有 Dog 类，使用对象类型表示如下：

```
Animal animal=new Dog();
Dogdog=(Dog)animal;    //向下转型，把 Animal 类型转换为 Dog 类型
Creature creature=animal;    //向上转型，把 Animal 类型转换为 Creature 类型
```

#### 例 1

下面通过具体的示例演示对象类型的转换。例如，父类 Animal 和子类 Cat 中都定义了实例变量 name、静态变量 staticName、实例方法 eat() 和静态方法 staticEat()。此外，子类 Cat 中还定义了实例变量 str 和实例方法 dogMethod()。

父类 Animal 的代码如下：

```
public class Animal
{
    public String name="Animal：动物";
    public static String staticName="Animal：可爱的动物";
    public void eat()
    {
        System.out.println("Animal：吃饭");
    }
    public static void staticEat()
    {
        System.out.println("Animal：动物在吃饭");
    }
}
```

子类 Cat 的代码如下：

```
public class Cat extends Animal
{
    public String name="Cat：猫";
    public String str="Cat：可爱的小猫";
    public static String staticName="Dog：我是喵星人";
    public void eat()
    {
        System.out.println("Cat：吃饭");
    }
    public static void staticEat()
    {
        System.out.println("Cat：猫在吃饭");
    }
    public void eatMethod()
    {
        System.out.println("Cat：猫喜欢吃鱼");
    }
    public static void main(String[] args)
    {
        Animal animal=new Cat();
        Cat cat=(Cat)animal;    //向下转型
        System.out.println(animal.name);    //输出 Animal 类的 name 变量
        System.out.println(animal.staticName);    // 输出 Animal 类的 staticName 变量
        animal.eat();    //输出 Cat 类的 eat()方法
        animal.staticEat();    //输出 Animal 类的 staticEat()方法
        System.out.println(cat.str);    //调用 Cat 类的 str 变量
        cat.eatMethod();    //调用 Cat 类的 eatMethod()方法
    }
}
```

通过引用类型变量来访问所引用对象的属性和方法时，Java 虚拟机将采用以下绑定规则：

*   实例方法与引用变量实际引用的对象的方法进行绑定，这种绑定属于动态绑定，因为是在运行时由 Java 虚拟机动态决定的。例如，animal.eat() 是将 eat() 方法与 Cat 类绑定。
*   静态方法与引用变量所声明的类型的方法绑定，这种绑定属于静态绑定，因为是在编译阶段已经做了绑定。例如，animal.staticEat() 是将 staticEat() 方法与 Animal 类进行绑定。
*   成员变量（包括静态变量和实例变量）与引用变量所声明的类型的成员变量绑定，这种绑定属于静态绑定，因为在编译阶段已经做了绑定。例如， animal.name 和 animal.staticName 都是与 Animal 类进行绑定。

对于 Cat 类，运行时将会输出如下结果：

```
Animal：动物
Animal：可爱的动物
Cat：吃饭
Animal：动物在吃饭
Cat：可爱的小猫
Cat：猫喜欢吃鱼
```

## 强制对象类型转换

Java 编译器允许在具有直接或间接继承关系的类之间进行类型转换。对于向下转型，必须进行强制类型转换；对于向上转型，不必使用强制类型转换。

例如，对于一个引用类型的变量，Java 编译器按照它声明的类型来处理。如果使用 animal 调用 str 和 eatMethod() 方法将会出错，如下：

```
animal.str="";    //编译出错，提示 Animal 类中没有 str 属性
animal.eatMethod();    //编译出错，提示 Animal 类中没有 eatMethod()方法
```

如果要访问 Cat 类的成员，必须通过强制类型转换，如下：

```
((Cat)animal).str="";    //编译成功
((Cat)animal).eatMethod();    //编译成功
```

把 Animal 对象类型强制转换为 Cat 对象类型，这时上面两句编译成功。对于如下语句，由于使用了强制类型转换，所以也会编译成功，例如：

```
Cat cat=(Cat)animal;    //编译成功，将 Animal 对象类型强制转换为 Cat 对象类型
```

子类的对象可以转换成父类类型，而父类的对象实际上无法转换为子类类型。因为通俗地讲，父类拥有的成员子类肯定也有，而子类拥有的成员，父类不一定有。因此，对于向上转型，不必使用强制类型转换。例如：

```
Cat cat=new Cat();
Animal animal=cat;    //向上转型，不必使用强制类型转换
```

如果两种类型之间没有继承关系，那么将不允许进行类型转换。例如：

```
Dog dog=new Dog();
Cat cat=(Cat)dog;    //编译出错，不允许把 Dog 对象类型转换为 Cat 对象类型
```