# Java 内部类：Java 静态内部类

静态内部类是指使用 static 修饰的内部类。示例代码如下：

```
public class Outer
{
    static class Inner
    {
        //静态内部类
    }
}
```

上述示例中的 Inner 类就是静态内部类。静态内部类有如下特点。

(1) 在创建静态内部类的实例时，不需要创建外部类的实例。

```
public class Outer
{
    static class Inner{}
}
class OtherClass
{
    Outer.Inner oi=new Outer.Inner();
}
```

(2) 静态内部类中可以定义静态成员和实例成员。外部类以外的其他类需要通过完整的类名访问静态内部类中的静态成员，如果要访问静态内部类中的实例成员，则需要通过静态内部类的实例。

```
public class Outer
{
    static class Inner
    {
        int a=0;    //实例变量 a
        static int b=0;    //静态变量 b
    }
}
class OtherClass
{
    Outer.Inner oi=new Outer.Inner();
    int a2=oi.a;    //访问实例成员
    int b2=Outer.Inner.b;    //访问静态成员
}
```

(3) 静态内部类可以直接访问外部类的静态成员，如果要访问外部类的实例成员，则需要通过外部类的实例去访问。

```
public class Outer
{
    int a=0;    //实例变量
    static int b=0;    //静态变量
    static class Inner
    {
        Outer o=new Outer;
        int a2=o.a;    //访问实例变量
        int b2=b;    //访问静态变量
    }
}
```