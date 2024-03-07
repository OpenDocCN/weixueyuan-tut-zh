# Java final 修饰符：final 修饰属性、final 修饰方法及 final 修饰类

final 关键字表示对象是最终形态的，对象是不可改变的意思。final 应用于类、方法和变量时意义是不同的，但本质是一样的：final 表示不可改变。

final 用在变量的前面表示变量的值不可以改变，此时该变量可以被称为常量；final 用在方法的前面表示方法不可以被重写；final 用在类的前面表示类不可以被继承，即该类是最终形态，如常见的 java.lang.String 类。

final 修饰符使用在如下方面：

#### 1\. final 修饰类中的属性

表示该属性一旦被初始化便不可改变，这里不可改变的意思对基本类型来说是其值不可变，而对对象属性来说其引用不可再变。其初始化可以在两个地方：一是其定义处，也就是说在 final 属性定义时直接给其赋值；二是在构造函数中。这两个地方只能选其一，要么在定义时给值，要么在构造函数中给值，不能同时既在定义时赋值，又在构造函数中赋予另外的值。

#### 2\. final 修饰类中的方法

说明这种方法提供的功能已经满足当前要求，不需要进行扩展，并且也不允许任何从此类继承的类来重写这种方法，但是继承仍然可以继承这个方法，也就是说可以直接使用。在声明类中，一个 final 方法只被实现一次。

#### 3\. final 修饰类

表示该类是无法被任何其他类继承的，意味着此类在一个继承树中是一个叶子类，并且此类的设计已被认为很完美而不需要进行修改或扩展。

对于 final 类中的成员，可以定义其为 final，也可以不是 final。而对于方法，由于所属类为 final 的关系，自然也就成了 final 型。也可以明确地给 final 类中的方法加上一个 final，这显然没有意义。

#### 例 1

下面创建一个示例演示 final 修饰符的具体使用。首先新建 FinalTest.java 文件，在该文件中创建 FinalTest 类，定义一个声明为 final 的 count 属性和一个声明为 final 的 sum() 方法。

FinalTest 类的代码如下：

```
public class FinalTest
{
    final int count=1;
    public int updateCount()
    {
        count=4;    //修改 final 属性值，提示错误
        return count;
    }
    public final int sum()
    {
        int number=count+10;
        return number;
    }
}
```

创建 FinalTest 类的子类 FinalExtendTest，在该类中重写父类 FinalTest 中的 sum() 方法，并继承父类中的 sum() 方法。代码如下：

```
public class FinalExtendTest extends FinalTest
{
    public int sum(){};    //重写父类 FinalTest 中的 sum()方法，出错
    int count=sum();    //继承父类 FinalTest 中的 sum()方法
}
```

最后在 FinalExtend 类中新建程序主方法——main() 方法，实例化 FinalExtendTest 类并访问该类的 count 属性，代码如下所示。

```
public static void main(String[] args)
{
    FinalExtendTest fet=new FinalExtendTest();
    System.out.println(fet.count);
}
```

在编译该文件时，会出现两处错误：一是代码“count=4;”处，此处试图给 count 变量重新赋值，会产生错误，因为 final 变量只能被初始化一次。第二处是代码“public int sum(){};”，此处试图重写 final 修饰的方法，会出现错误，因为 final 修饰的方法可以被继承但不能被任何类重写。

将两处的错误语句都注释掉，程序运行后输出的结果如下：

```
11
```