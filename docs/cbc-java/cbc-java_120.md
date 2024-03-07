# Java 类的继承：Java 简单的继承及单继承和多继承的区别

继承是代码复用的一种形式，即在具有包含关系的类中，从属类继承主类的全部属性和方法，从而减少了代码冗余，提高了程序运行效率。例如，一个矩形（Rectangle 类）属于四边形（Quadrilateral），正方形、平行四边形和梯形同样都属于四边形。从类的角度来解释，可以说成 Rectangle 类是从 Quadrilateral 类继承而来的，其中 Quadrilateral 类是基类，Rectangle 类是派生类。

## 简单继承

Java 中类的继承是通过扩展其他类而形成新类来实现的，原来的类称为父类（super class）或基类，新类称为原来类的子类或派生类。在子类中，不仅包含父类的属性和方法，还可以增加新的属性和方法，使得父类的基本特征可被所有子类的对象共享。

注意：类的继承并不改变类成员的访问权限。也就是说，如果父类的成员是公有的、被保护的或默认的，它的子类仍具有相应的这些特性。

类继承的定义格式如下：

```
class class_name extends extend_class
{
    //类的主体
}
```

其中，class_name 表示子类（派生类）的名称；extend_class 表示父类（基类）的名称；extends 关键字直接跟在子类名之后，其后面是该类要继承的父类名称。例如：

```
public class Student extends Person{}
```

#### 例 1

教师类和学生类可以由人类派生，他们具有共同的属性：姓名、年龄、性别、身份证号，而学生还具有学生号和所学专业两个属性，教师还具有教龄和所教专业两个属性。下面编写 Java 程序代码，使教师类和学生类都继承于人类，具体的实现步骤如下。

(1) 创建人类 People，并定义 name、age、sex、sn 属性，代码如下：

```
public class People
{
    public String name;    //姓名
    public int age;    //年龄
    public String sex;    //性别
    public String sn;    //身份证号
    public People(String name,int age,String sex,String sn)
    {
        this.name=name;
        this.age=age;
        this.sex=sex;
        this.sn=sn;
    }
    public String toString()
    {
        return"姓名："+name+"\n 年龄："+age+"\n 性别："+sex+"\n 身份证号："+sn;
    }
}
```

如上述代码，在 People 类中包含 4 个公有属性、一个构造方法和一个 toString() 方法。

(2) 创建 People 类的子类 Student 类，并定义 stuNo 和 department 属性，代码如下：

```
public class Student extends People
{
    private String stuNo;    //学号
    private String department;    //所学专业
    public Student(String name,int age,String sex,String sn,String stuno,String department)
    {
        super(name,age,sex,sn);    //调用父类中的构造方法
        this.stuNo=stuno;
        this.department=department;
    }
    public String toString()
    {
        return"姓名："+name+"\n 年龄："+age+"\n 性别："+sex+"\n 身份证号："+sn+"\n 学号："+stuNo+"\n 所学专业："+department;
    }
}
```

由于 Student 类继承自 People 类，因此，在 Student 类中同样具有 People 类的属性和方法，这里重写了父类中的 toString() 方法。

注意：如果在父类中存在有参的构造方法而并没有重载无参的构造方法，那么在子类中必须含有有参的构造方法，因为如果在子类中不含有构造方法，默认会调用父类中无参的构造方法，而在父类中并没有无参的构造方法，因此会出错。

(3) 创建 People 类的另一个子类 Teacher，并定义 tYear 和 tDept 属性，代码如下：

```
public class Teacher extends People
{
    private int tYear;    //教龄
    private String tDept;    //所教专业
    public Teacher(String name,int age,String sex,String sn,int tYear,String tDept)
    {
        super(name,age,sex,sn);    //调用父类中的构造方法
        this.tYear=tYear;
        this.tDept=tDept;
    }
    public String toString()
    {
        return"姓名："+name+"\n 年龄："+age+"\n 性别:"+sex+"\n 身份证号："+sn+"\n 教龄："+tYear+"\n 所教专业："+tDept;
    }
}
```

Teacher 类与 Student 类相似，同样重写了父类中的 toString() 方法。

(4) 编写测试类 PeopleTest，在该类中创建 People 类的不同对象，分别调用它们的 toString() 方法，输出不同的信息。具体的代码如下：

```
public class PeopleTest
{
    public static void main(String[] args)
    {
        //创建 Student 类对象
        People stuPeople=new Student("王丽丽",23,"女","410521198902145589","00001","计算机应用与技术");
        System.out.println("----------------学生信息---------------------");
        System.out.println(stuPeople);

        //创建 Teacher 类对象
        People teaPeople=new Teacher("张文",30,"男","410521198203128847",5,"计算机应用与技术");
        System.out.println("----------------教师信息----------------------");
        System.out.println(teaPeople);
    }
}
```

运行程序，输出的结果如下：

```
----------------学生信息---------------------
姓名：王丽丽
年龄：23
性别：女
身份证号：410521198902145589
学号：00001
所学专业：计算机应用与技术
----------------教师信息----------------------
姓名：张文
年龄：30
性别:男
身份证号：410521198203128847
教龄：5
所教专业：计算机应用与技术
```

## 单继承

Java 不支持多继承，只允许一个类直接继承另一个类，即子类只能有一个父类，extends 关键字后面只能有一个类名。例如，如下代码会导致编译错误：

```
class Student extends Person,Person1,Person2{…}
class Student extends Person,extends Person1,extends Person2{…}
```

尽管一个类只能有一个直接的父类，但是它可以有多个间接的父类。例如，Student 类继承 Person 类，Person 类继承 Person1 类，Person1 类继承 Person2 类，那么 Person1 和 Person2 类是 Student 类的间接父类。图 1 展示了单继承的关系。

![](img/c9dd519c447d0418489ddf7128ec6f19.jpg)
图 1 图形类之间的关系
从图 1 中可以看出，三角形、四边形和五边形的直接父类是多边形类，它们的间接父类是图形类。图形类、多边形类和三角形、四边形、五边形类形成了一个继承的分支。在这个分支上，位于下层的子类会继承上层所有直接或间接父类的属性和方法。如果两个类不在同一个继承树分支上，就不会存在继承关系，例如多边形类和直线。

提示：间接的继承关系就是分别继承、实现，例如：class Graph extends Polygon{…}、class Trigon extends Graph{…}。如果类声明头部没有定义任何 extends 子句，则该类隐含地继承自 java.lang.Object 类。