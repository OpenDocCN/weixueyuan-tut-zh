# Java 创建一个学生类

创建一个表示学生的实体类 Student，其中有学生姓名、性别和年龄信息。要求使用属性来表示学生信息，最终编写测试代码。

首先定义一个名为 Student 的类，代码如下：

```
public class Student
{
    //学生类
}
```

在类中通过属性定义学生、性别和年龄，代码如下：

```
public class Student
{
    public String Name;    //学生姓名
    public int Age;    //学生年龄
    private boolean Sex;    //学生性别
}
```

在上述代码中将学生性别属性 Sex 设置为 private 作用域。为了对该属性进行获取和设置，还需要编写 isSes 和 setSex 方法。代码如下：

```
public boolean isSex()
{
    return Sex;
}
public void setSex(boolean sex)
{
    this.Sex=sex;
}
```

在 Student 类中添加 main() 方法，然后创建两个学生类的实例，并输出学生信息。最终代码如下：

```
public static void main(String[] args)
{
    Student zhang=new Student();    //创建第一个实例
    zhang.Name="张子同";
    String isMan=zhang.isSex()?"女":"男";
    System.out.println("姓名："+zhang.Name+"性别："+isMan+"年龄："+zhang.Age);
    Student li=new Student();    //创建第二个实例
    li.Name="李子文";
    li.Sex=true;
    li.Age=15;
    String isWoman=li.isSex()?"女":"男";
    System.out.println("姓名："+li.Name+"性别："+isWoman+"年龄："+li.Age);
}
```

输出结果如下：

```
姓名：张子同性别：男年龄：0
姓名：李子文性别：女年龄：15
```

由输出结果可以看到，在第一个实例 zhang 中由于仅设置了 Name 属性的值，所以 boolean 类型的 Sex 默认使用值 false，int 类型的 Age 默认使用值 0。第二个实例 li 同时设置了这三个属性的值。