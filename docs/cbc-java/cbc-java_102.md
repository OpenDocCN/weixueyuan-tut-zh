# Java this 关键字

this 关键字是 Java 常用的关键字，可用于任何实例方法内指向当前对象，也可指向对其调用当前方法的对象，或者在需要当前类型对象引用时使用。

#### 例 1

假设有一个教师类 Teacher 的定义如下：

```
public class Teacher
{
    private String name;    //教师名称
    private double salary;    //工资
    private int age;    //年龄
}
```

在上述代码中 name、salary 和 age 的作用域是 private，因此在类外部无法对它们的值进行设置。为了解决这个问题，可以为 Teacher 类添加一个构造方法，然后在构造方法中传递参数进行修改。代码如下：

```
//创建构造方法，为上面的 3 个属性赋初始值
public Teacher(String name,double salary,int age)
{
    this.name=name;    //设置教师名称
    this.salary=salary;    //设置教师工资
    this.age=age;    //设置教师年龄
}
```

在 Teacher 类的构造方法中使用了 this 关键字对属性 name、salary 和 age 赋值，this 表示当前对象。this.name=name 语句表示一个赋值语句，等号左边的 this.name 是指当前对象具有的变量 name，等号右边的 name 表示参数传递过来的数值。

创建一个 main() 方法对 Teacher 类进行测试，代码如下：

```
public static void main(String[] args)
{
    Teacher teacher=new Teacher("王刚",5000.0,45);
    System.out.println("教师信息如下：");
    System.out.println("教师名称："+teacher.name+"\n 教师工资："+teacher.salary+"\n 教师年龄："+teacher.age);
}
```

运行该程序，输出的结果如下所示。

```
教师信息如下：
教师名称：王刚
教师工资：5000.0
教师年龄：45
```

提示：当一个类的属性（成员变量）名与访问该属性的方法参数名相同时，则需要使用 this 关键字来访问类中的属性，以区分类的属性和方法中的参数。