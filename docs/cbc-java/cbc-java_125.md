# Java 多态性：Java 什么是多态？

多态性是面向对象编程的又一个重要特征，它是指在父类中定义的属性和方法被子类继承之后，可以具有不同的数据类型或表现出不同的行为，这使得同一个属性或方法在父类及其各个子类中具有不同的含义。

对面向对象来说，多态分为编译时多态和运行时多态。其中编译时多态是静态的，主要是指方法的重载，它是根据参数列表的不同来区分不同的方法。通过编译之后会变成两个不同的方法，在运行时谈不上多态。而运行时多态是动态的，它是通过动态绑定来实现的，也就是大家通常所说的多态性。

Java 实现多态有 3 个必要条件：继承、重写和向上转型。只有满足这 3 个条件，开发人员才能够在同一个继承结构中使用统一的逻辑实现代码处理不同的对象，从而执行不同的行为。

*   继承：在多态中必须存在有继承关系的子类和父类。
*   重写：子类对父类中某些方法进行重新定义，在调用这些方法时就会调用子类的方法。
*   向上转型：在多态中需要将子类的引用赋给父类对象，只有这样该引用才既能可以调用父类的方法，又能调用子类的方法。

#### 例 1

下面通过一个例子来演示重写如何实现多态性。例子使用了类的继承和运行时多态机制，具体步骤如下。

(1) 创建 Figure 类，在该类中首先定义存储二维对象的尺寸，然后定义有两个参数的构造方法，最后添加 area() 方法，该方法计算对象的面积。代码如下：

```
public class Figure
{
    double dim1;
    double dim2;
    Figure(double d1,double d2)
    {
        //有参的构造方法
        this.dim1=d1;
        this.dim2=d2;
    }
    double area()
    {
        //用于计算对象的面积
        System.out.println("父类中计算对象面积的方法，没有实际意义，需要在子类中重写。");
        return 0;
    }
}
```

(2) 创建继承自 Figure 类的 Rectangle 子类，该类调用父类的构造方法，并且重写父类中的 area() 方法。代码如下：

```
public class Rectangle extends Figure
{
    Rectangle(double d1,double d2)
    {
        super(d1,d2);
    }
    double area()
    {
        System.out.println("长方形的面积：");
        return super.dim1*super.dim2;
    }
}
```

(3) 创建继承自 Figure 类的 Triangle 子类，该类与 Rectangle 相似。代码如下：

```
public class Triangle extends Figure
{
    Triangle(double d1,double d2)
    {
        super(d1,d2);
    }
    double area()
    {
        System.out.println("三角形的面积：");
        return super.dim1*super.dim2/2;
    }
}
```

(4) 创建 Test 测试类，在该类的 main() 方法中首先声明 Figure 类的变量 figure，然后分别为 figure 变量指定不同的对象，并调用这些对象的 area() 方法。代码如下：

```
public class Test
{
    public static void main(String[] args)
    {
        Figure figure;    //声明 Figure 类的变量
        figure=new Rectangle(9,9);
        System.out.println(figure.area());
        System.out.println("===============================");
        figure=new Triangle(6,8);
        System.out.println(figure.area());
        System.out.println("===============================");
        figure=new Figure(10,10);
        System.out.println(figure.area());
    }
}
```

从上述代码可以发现，无论 figure 变量的对象是 Rectangle 还是 Triangle，它们都是 Figure 类的子类，因此可以向上转型为该类，从而实现多态。

(5) 执行上述代码，输出结果如下：

```
长方形的面积：
81.0
===============================
三角形的面积：
24.0
===============================
父类中计算对象面积的方法，没有实际意义，需要在子类中重写。
0.0
```