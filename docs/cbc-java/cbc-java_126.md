# Java 抽象（abstract）类

如果一个类只定义了一个为所有子类共享的一般形式，至于细节则交给每一个子类去实现，这种类没有任何具体的实例，只具有一些抽象的概念，那么这样的类称为抽象类。

在面向对象领域，抽象类主要用来进行类型隐藏。比如，如果我们进行一个图形编辑软件的开发，就会发现问题领域存在着圆、三角形这样一些具体概念，它们是不同的，但是它们都属于形状这样一个概念，形状这个概念在问题领域是不存在的，它就是一个抽象概念。正是因为抽象概念在问题领域没有对应的具体概念，所以用以表征抽象概念的抽象类是不能够实例化的。

在 Java 中抽象类的语法格式如下：

```
<abstract>class<class_name>
{
    <abstract><type><method_name>(parameter-iist);
}
```

其中，abstract 表示该类或该方法是抽象的；class_name 表示抽象类的名称；method_name 表示抽象方法名称，如果在一个方法之前使用 abstract 来修饰，则说明该方法是抽象方法，不能有方法体；parameter-list 表示方法参数列表。

注意：abstract 关键字只能用于普通方法，不能用于 static 方法或者构造方法中。在抽象类中必须包含至少一个抽象方法，并且所有抽象方法不能有具体的实现，而应在它们的子类中实现所有的抽象方法（要有方法体）。

包含一个或多个抽象方法的类必须通过在其 class 声明前添加 abstract 关键字将其声明为抽象类。因为一个抽象类不定义完整的实现，所以抽象类也就没有自己的对象。因此，任何使用 new 创建抽象类对象的尝试都会导致编译时错误。

#### 例 1

不同几何图形的面积计算公式是不同的，但是它们具有的特性是相同的，都具有长和宽这两个属性，也都具有面积计算的方法。那么可以定义一个抽象类，在该抽象类中含有两个属性（width 和 height）和一个抽象方法 area()，具体步骤如下。

(1) 首先创建一个表示图形的抽象类 Shape，代码如下所示。

```
public abstract class Shape
{
    public int width;    //几何图形的长
    public int height;    //几何图形的宽
    public Shape(int width,int height)
    {
        this.width=width;
        this.height=height;
    }
    public abstract double area();    //定义抽象方法，计算面积
}
```

(2) 定义一个正方形类，该类继承自形状类 Shape，并重写了 area() 抽象方法。正方形类的代码如下：

```
public class Square extends Shape
{
    public Square(int width,int height)
    {
        super(width,height);
    }
    //重写父类中的抽象方法，实现计算正方形面积的功能
    @Override
    public double area()
    {
        return width*height;
    }
}
```

(3) 定义一个三角形类，该类与正方形类一样，需要继承形状类 Shape，并重写父类中的抽象方法 area()。三角形类的代码实现如下：

```
public class Triangle extends Shape
{
    public Triangle(int width,int height)
    {
        super(width, height);
    }
    //重写父类中的抽象方法，实现计算三角形面积的功能
    @Override
    public double area()
    {
        return 0.5*width*height;
    }
}
```

(4) 最后创建一个测试类，分别创建正方形类和三角形类的对象，并调用各类中的 area() 方法，打印出不同形状的几何图形的面积。测试类的代码如下：

```
public class ShapeTest
{
    public static void main(String[] args)
    {
        Square square=new Square(5,4);    //创建正方形类对象
        System.out.println("正方形的面积为："+square.area());
        Triangle triangle=new Triangle(2,5);    //创建三角形类对象
        System.out.println("三角形的面积为："+triangle.area());
    }
}
```

在该程序中，创建了 4 个类，分别为图形类 Shape、正方形类 Square、三角形类 Triangle 和测试类 ShapeTest。其中图形类 Shape 是一个抽象类，创建了两个属性，分别为图形的长度和宽度，并通过构造方法 Shape() 给这两个属性赋值。

在 Shape 类的最后定义了一个抽象方 法 area()，用来计算图形的面积。在这里，Shape 类只是定义了计算图形面积的方法，而对于如何计算并没有任何限制。也可以这样理解，抽象类 Shape 仅定义了子类的一般形式。

正方形类 Square 继承抽象类 Shape，并实现了抽象方法 area()。三角形类 Triangle 的实现和正方形类相同，这里不再介绍。

在测试类 ShapeTest 的 main() 方法中，首先创建了正方形类和三角形类的实例化对象 square 和 triangle，然后分别调用 area() 方法实现了面积的计算功能。

(5) 运行该程序，输出的结果如下：

```
正方形的面积为：20.0
三角形的面积为：5.0
```