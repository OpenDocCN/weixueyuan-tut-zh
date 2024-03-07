# Java 匿名类，Java 匿名内部类

匿名类是指没有类名的内部类，必须在创建时使用 new 语句来声明类。其语法形式如下：

```
new<类或接口>()
{
    //类的主体
};
```

这种形式的 new 语句声明一个新的匿名类，它对一个给定的类进行扩展，或者实现一个给定的接口。使用匿名类可使代码更加简洁、紧凑，模块化程度更高。

匿名类有两种实现方式：

*   继承一个类，重写其方法。
*   实现一个接口（可以是多个），实现其方法。

下面通过代码来说明。

```
public class Out
{
    void show()
    {
        System.out.println("调用 Out 类的 show() 方法");
    }
}
public class TestAnonymousInterClass
{
    //在这个方法中构造一个匿名内部类
    private void show()
    {
        Out anonyInter=new Out()
        {
            //获取匿名内部类的实例
            void show()
            {
                System.out.println("调用匿名类中的 show() 方法");
            }
        };
        anonyInter.show();
    }
    public static void main(String[] args)
    {
        TestAnonymousInterClass test=new TestAnonymousInterClass();
        test.show();
    }
}
```

程序的输出结果如下：

```
调用匿名类中的 show() 方法
```

从输出结果可以看出，匿名内部类有自己的实现。

提示：匿名内部类实现一个接口的方式与实现一个类的方式相同，这里不再赘述。

匿名类有如下特点。

(1) 匿名类和局部内部类一样，可以访问外部类的所有成员。如果匿名类位于一个方法中，则匿名类只能访问方法中 final 类型的局部变量和参数。

```
public static void main(String[] args)
{
    int a=10;
    final int b=10;
    Out anonyInter=new Out()
    {
        void show()
        {
            //System.out.println("调用了匿名类的 show() 方法"+a);    //编译出错
            System.out.println("调用了匿名类的 show() 方法"+b);    //编译通过
        }
    };
    anonyInter.show();
}
```

(2) 匿名类中允许使用非静态代码块进行成员初始化操作。

```
Out anonyInter=new Out()
{
    int i;
    {    //非静态代码块
        i=10;    //成员初始化
    }
    public void show()
    {
        System.out.println("调用了匿名类的 show() 方法"+i);
    }
};
```

(3) 匿名类的非静态代码块会在父类的构造方法之后被执行。