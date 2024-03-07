# Java 方法的可变参数

在具体实际开发过程中，有时方法中参数的个数是不确定的。为了解决这个问题，在 J2SE 5.0 版本中引入了可变参数的概念。

声明可变参数的语法格式如下：

```
methodName({paramList},paramType…paramName)
```

其中，methodName 表示方法名称；paramList 表示方法的固定参数列表；paramType 表示可变参数的类型；… 是声明可变参数的标识；paramName 表示可变参数名称。

注意：可变参数必须定义在参数列表的最后。

#### 例 1

每次参加考试的人数是不固定的，但是每次考试完之后都需要打印出本次考试的总人数以及参加考试的学生名单。下面编写程序，使用方法的可变参数实现该功能，具体的代码如下：

```
public class StudentTestMethod
{
    //定义输出考试学生的人数及姓名的方法
    public void print(String...names)
    {
        int count=names.length;    //获取总个数
        System.out.println("本次参加考试的有"+count+"人，名单如下：");
        for(int i=0;i<names.length;i++)
        {
            System.out.println(names[i]);
        }
    }
    public static void main(String[] args)
    {
        //TODO Auto-generated method stub
        StudentTestMethod student=new StudentTestMethod();
        student.print("张强","李成","王勇");    //传入 3 个值
        student.print("马丽","陈玲");
    }
}
```

在 Student TestMethod 类中定义了 print() 方法和 main() 方法。print() 方法声明了一个 String 类型的可变参数，方法体打印可变参数的总个数以及参数值。在 main() 方法中创建了 StudentTestMethod 类的实例，然后分别传入不同个数的参数调用 print() 方法。

运行 StudentTestMethod 类，输出结果如下：

```
本次参加考试的有 3 人，名单如下：
张强
李成
王勇
本次参加考试的有 2 人，名单如下：
马丽
陈玲
```