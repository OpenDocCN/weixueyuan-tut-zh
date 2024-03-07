# Java 查询个人信息

每个员工都会有自己的档案，主管可以查看在职员工的档案。使用 Java 创建一个员工实体类，然后通过构造方法创建一个名为“王洁”的员工，最后打印出员工档案信息。示例步骤如下。

(1) 创建 Person 类，在该类中定义个人基本信息属性，并定义一个带有参数的构造方法，代码如下：

```
public class Person
{
    private String name;    //姓名
    private int age;    //年龄
    private String sex;    //性别
    private String birthday;    //出生日期
    private String constellation;    //星座
    public Person(String name,int age,String sex,String birthday,String constellation)
    {
        this.name=name;
        this.age=age;
        this.sex=sex;
        this.birthday=birthday;
        this.constellation=constellation;
    }
    public String intro()
    {
        return"姓名："+name+"\n 年龄："+age+"\n 性别："+sex+"\n 出生日期："+birthday+"\n 星痤："+constellation;
    }
}
```

在 Person 类中，首先声明了 5 个修饰符为 private 的成员变量（属性），然后定义了 Person 类的构造方法，该构造方法中需要传递 5 个参数，并将传递的这 5 个参数值赋给该类中的 5 个成员变量。接着创建了 intro() 方法，返回个人基本信息内容。

(2) 创建 PersonTest 类，并在该类中创建 main() 方法作为该程序的入口。在 main() 方法中使用 Person 类的构造方法对其属性进行初始化，并调用 intro() 方法，输出个人基本信息。代码如下：

```
public class PersonTest
{
    public static void main(String[] args)
    {
        Person person=new Person("王洁",21,"女","2016-02-21","獅子座");
        String intro=person.intro();
        System.out.println(intro);
    }
}
```

在 TestPerson 类中调用了 Person 类的构造方法，并调用了 intro() 方法，从而完成了打印个人基本信息的功能。运行 TestPerson 类，打印出的个人基本信息如下：

```
姓名：王洁
年龄：21
性别：女
出生日期：2016-02-21
星痤：獅子座
```