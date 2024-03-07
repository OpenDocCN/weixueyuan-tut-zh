# Java 内部类：Java 局部内部类

局部内部类是指在一个方法中定义的内部类。示例代码如下：

```
public class Test
{
    public void method()
    {
        class Inner
        {
            //局部内部类
        }
    }
}
```

局部内部类有如下特点。

(1) 局部内部类与局部变量一样，不能使用访问控制修饰符（public、private 和 protected）和 static 修饰符修饰。

(2) 局部内部类只在当前方法中有效。

```
public class Test
{
    Inner i=new Inner();    //编译出错
    Test.Inner ti=new Test.Inner();    //编译出错
    Test.Inner ti2=new Test().new Inner();    //编译出错
    public void method()
    {
        class Inner{}
        Inner i=new Inner();
    }
}
```

(3) 局部内部类中不能定义 static 成员。

(4) 局部内部类中还可以包含内部类，但是这些内部类也不能使用访问控制修饰符（public、 private 和 protected）和 static 修饰符修饰。

(5) 在局部内部类中可以访问外部类的所有成员。

(6) 在局部内部类中只可以访问当前方法中 final 类型的参数与变量。如果方法中的成员与外部类中的成员同名，则可以使用 <OuterClassName>.this.<MemberName> 的形式访问外部类中的成员。

```
public class Test
{
    int a=0;
    int d=0;
    public void method()
    {
        int b=0;
        final int c=0;
        final int d=10;
        class Inner
        {
            a2=a;    //访问外部类中的成员
            //intb2=b;    //编译出错
            int c2=c;    //访问方法中的成员
            int d2=d;    //访问方法中的成员
            int d3=Test.this.d;    //访问外部类中的成员
        }
        Inner i=new Inner();
        System.out.println(i.d2);    //输出 10
        System.out.printIn(i.d3);    //输出 0
    }
    public static void main(String[] args)
    {
        Test t=new Test();
        t.method();
    }
}
```