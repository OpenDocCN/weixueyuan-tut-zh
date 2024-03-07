# Java 内部类：Java 实例内部类

实例内部类是指没有用 static 修饰的内部类。示例代码如下：

```
public class Outer
{
    class Inner
    {
        //实例内部类
    }
}
```

上述示例中的 Inner 类就是实例内部类。实例内部类有如下特点。

(1) 在外部类的静态方法和外部类以外的其他类中，必须通过外部类的实例创建内部类的实例。

```
public class Outer
{
    class Inner1{}
    Inner1 i=new Inner1();    //不需要创建外部类实例
    public void method1()
    {
        Inner1 i=new Inner1();    //不需要创建外部类实例
    }
    public static void method2()
    {
        Inner1 i=new Outer().new inner1();    //需要创建外部类实例
    }
    class Inner2
    {
        Inner1 i=new Inner1();    //不需要创建外部类实例
    }
}
class OtherClass
{
    Outer.Inner i=new Outer().new Inner();    //需要创建外部类实例
}
```

(2) 在实例内部类中，可以访问外部类的所有成员。

```
public class Outer
{
    public int a=100;
    static int b=100;
    final int c=100;
    private int d=100;
    public String method1()
    {
        return "实例方法 1";
    }
    public static String method2()
    {
        return "静态方法 2";
    }
    class Inner
    {
        int a2=a+1;    //访问 public 的 a
        int b2=b+1;    //访问 static 的 b
        int c2=c+1;    //访问 final 的 c
        int d2=d+1;    //访问 private 的 d
        String str1=method1();    //访问实例方法 method1
        String str2=method2();    //访问静态方法 method2
    }
    public static void main(String[] args)
    {
        Inner i=new Outer().new Inner();    //创建内部类实例
        System.out.println(i.a2);    //输出 101
        System.out.println(i.b2);    //输出 101
        System.out.println(i.c2);    //输出 101
        System.out.println(i.d2);    //输出 101
        System.out.println(i.strl);    //输出实例方法 1
        System.out.println(i.str2);    //输出静态方法 2
    }
}
```

提示：如果有多层嵌套，则内部类可以访问所有外部类的成员。

(3) 在外部类中不能直接访问内部类的成员，而必须通过内部类的实例去访问。如果类 A 包含内部类 B，类 B 中包含内部类 C，则在类 A 中不能直接访问类 C，而应该通过类 B 的实例去访问类 C。

(4) 外部类实例与内部类实例是一对多的关系，也就是说一个内部类实例只对应一个外部类实例，而一个外部类实例则可以对应多个内部类实例。

如果实例内部类 B 与外部类 A 包含有同名的成员 t，则在类 B 中 t 和 this.t 都表示 B 中的成员 t，而 A.this.t 表示 A 中的成员 t。

```
public class Outer
{
    int a=10;
    class Inner
    {
        int a=20;
        int b1=a;
        int b2=this.a;
        int b3=Outer.this.a;
    }
    public static void main(String[] args)
    {
        Inner i=new Outer().new Inner();
        System.out.println(i.b1);    //输出 20
        System.out.println(i.b2);    //输出 20
        System.out.println(i.b3);    //输出 10
    }
}
```

(5) 在实例内部类中不能定义 static 成员，除非同时使用 final 和 static 修饰。