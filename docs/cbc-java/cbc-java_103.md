# Java 创建对象详解：Java 创建对象的几种方法

对象是对类的实例化。对象具有状态和行为，变量用来表明对象的状态，方法表明对象所具有的行为。Java 对象的生命周期包括创建、使用和清除，本文详细介绍对象的创建，在 Java 语言中创建对象分显式创建与隐含创建两种情况。

## 显式创建对象

对象的显式创建方式有 4 种。

#### 1\. 使用 new 关键字创建对象

这是常用的创建对象的方法，语法格式如下：

```
类名 对象名=new 类名()；
```

#### 2\. 调用 java.lang.Class 或者 java.lang.reflect.Constuctor 类的 newlnstance() 实例方法

在 Java 中，可以使用 java.lang.Class 或者 java.lang.reflect.Constuctor 类的 newlnstance() 实例方法来创建对象，代码格式如下：

```
java.lang.Class Class 类对象名称=java.lang.Class.forName(要实例化的类全称);
类名 对象名=(类名)Class 类对象名称.newInstance();
```

调用 java.lang.Class 类中的 forName() 方法时，需要将要实例化的类的全称（比如 com.mxl.package.Student）作为参数传递过去，然后再调用 java.lang.Class 类对象的 newInstance() 方法创建对象。

#### 3\. 调用对象的 clone() 方法

该方法不常用，使用该方法创建对象时，要实例化的类必须继承 java.lang.Cloneable 接口。 调用对象的 clone() 方法创建对象的语法格式如下：

```
类名对象名=(类名)已创建好的类对象名.clone();
```

#### 
4\. 调用 java.io.ObjectlnputStream 对象的 readObject() 方法

#### 例 1

下面创建一个示例演示常用的前三种对象创建方法。示例代码如下：

```
public class Student implements Cloneable
{   
    //实现 Cloneable 接口
    private String Name;    //学生名字
    private int age;    //学生年龄
    public Student(String name,int age)
    {    //构造方法
        this.Name=name;
        this.age=age;
    }
    public Student()
    {
        this.Name="name";
        this.age=0;
    }
    public String toString()
    {
        return"学生名字："+Name+"，年龄："+age;
    }
    public static void main(String[] args)throws Exception
    {
        System.out.println("---------使用 new 关键字创建对象---------");

        //使用 new 关键字创建对象
        Student student1=new Student("小刘",22);
        System.out.println(student1);
        System.out.println("-----------调用 java.lang.Class 的 newInstance() 方法创建对象-----------");

        //调用 java.lang.Class 的 newInstance() 方法创建对象
        Class cl=Class.forName("Student");
        Student student2=(Student)cl.newInstance();
        System.out.println(student2);
        System.out.println("-------------------调用对象的 clone() 方法创建对象----------");

        //调用对象的 clone() 方法创建对象
        Student student3=(Student)student2.clone();
        System.out.println(student3);
    }
}
```

对上述示例的说明如下：

*   使用 new 关键字或 Class 对象的 newInstance() 方法创建对象时，都会调用类的掏造方法。
*   使用 Class 类的 newInstance() 方法创建对象时，会调用类的默认构造方法，即无参构造方法。
*   使用 Object 类的 clone() 方法创建对象时，不会调用类的构造方法，它会创建一个复制的对象，这个对象和原来的对象具有不同的内存地址，但它们的属性值相同。
*   如果类没有实现 Cloneable 接口，则 clone。方法会抛出 java.lang.CloneNotSupportedException 异常，所以应该让类实现 Cloneable 接口。

程序执行结果如下：

```
---------使用 new 关键字创建对象---------
学生名字：小刘，年龄：22
-----------调用 java.lang.Class 的 newInstance() 方法创建对象-----------
学生名字：name，年龄：0
-------------------调用对象的 done()方法创建对象----------
学生名字：name，年龄：0
```

## 隐含创建对象

除了显式创建对象以外，在 Java 程序中还可以隐含地创建对象，例如下面几种情况。
①String strName="strValue"，其中的“strValue”就是一个 String 对象，由 Java 虚拟机隐含地创建。
②字符串的“+”运算符运算的结果为一个新的 String 对象，示例如下：

```
String str1="Hello";
String str2="Java";
String str3=str1+str2;    //str3 引用一个新的 String 对象
```

③当 Java 虚拟机加载一个类时，会隐含地创建描述这个类的 Class 实例。

提示：类的加载是指把类的 .class 文件中的二进制数据读入内存中，把它存放在运行时数据区的方法区内，然后在堆区创建一个 java.lang.Class 对象，用来封装类在方法区内的数据结构。

无论釆用哪种方式创建对象，Java 虚拟机在创建一个对象时都包含以下步骤：
1) 给对象分配内存。
2) 将对象的实例变量自动初始化为其变量类型的默认值。
3) 初始化对象，给实例变量赋予正确的初始值。

注意：每个对象都是相互独立的，在内存中占有独立的内存地址，并且每个对象都具有自己的生命周期，当一个对象的生命周期结束时，对象就变成了垃圾，由 Java 虚拟机自带的垃圾回收机制处理。