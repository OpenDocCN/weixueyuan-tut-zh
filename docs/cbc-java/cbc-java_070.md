# Java Object 类详解：Object 类的常用方法（equals()和 getClass()）

Object 是 Java 类库中的一个特殊类，也是所有类的父类。当一个类被定义后，如果没有指定继承的父类，那么默认父类就是 Object 类。因此，以下两个类是等价的。

```
public class MyClass{…}
```

等价于

```
public class MyClass extends Object {…}
```

由于 Java 中的所有类都是由 Object 类派生出来的，因此在 Object 类中定义的方法，在其他类中都可以使用，常见的方法如表 1 所示。

表 1 Object 类的常用方法

| 方法 | 说明 |
| Object clone() | 创建与该对象的类相同的新对象 |
| boolean equals(Object) | 比较两对象是否相等 |
| void finalize() | 当垃圾回收器确定不存在对该对象的更多引用时，对象的圾回收器调用该方法 |
| Class getClass() | 返回一个对象运行时的实例类 |
| int hashCode() | 返回该对象的散列码值 |
| void notify() | 激活等待在该对象的监视器上的一个线程 |
| void notifyAll() | 激活等待在该对象的监视器上的全部线程 |
| String toString() | 返回该对象的字符串表示 |
| void wait() | 在其他线程调用此对象的 notify() 方法或 notifyAll() 方法前，导致当前线程等待 |

其中，equals() 方法和 getClass() 方法在 Java 程序中比较常用。

## equals() 方法

equals() 方法的作用与运算符类似，用于值与值的比较和值与对象的比较，而 equals() 方法用于对象与对象之间的比较，其使用格式如下：

```
boolean result=obj.equals(Object o);
```

其中，Obj 表示要进行比较的一个对象，o 表示另一个对象。

#### 例 1

编写一个 Java 程序，实现用户登录的验证功能。要求，用户从键盘输入登录用户名和密码，当用户输入的用户名等于 admin 并且密码也等于 admin 时，则表示该用户为合法用户，提示登录成功，否则提示用户名或者密码错误信息。

在这里使用 equals() 方法将用户输入的字符串与保存 admin 的字符串对象进行比较，具体的代码如下：

```
import java.util.Scanner;
public class Test01
{
    //验证用户名和密码
    public static boolean validateLogin(String uname,String upwd)
    {
        boolean con=false;
        if(uname.equals("admin")&&upwd.equals("admin"))
        {    //比较两个 String 对象
            con=true;
        }
        else
        {
            con=false;
        }
        return con;
    }
    public static void main(String[] args)
    {
        Scanner input=new Scanner(System.in);
        System.out.println("------欢迎使用大数据管理平台------");
        System.out.println("用户名：");
        String username=input.next();    //获取用户输入的用户名
        System.out.printin("密码：");
        String pwd=input.next();    //获取用户输入的密码
        boolean con=validateLogin(username,pwd);
        if(con)
        {
            System.out.println("登录成功！");
        }
        else
        {
            System.out.println("用户名或密码有误！");
        }
    }
}
```

上述代码在 validateLogin() 方法中又使用 equals() 方法将两个 String 类型的对象进行了比较，当 uname 对象与保存 admin 的 String 对象相同时，uname.equals("admin") 为 true；与此相同，当 upwd 对象与保存 admin 的 String 对象相同时，upwd.equals("admin") 为 true。当用户输入的用户名和密码都为 admin 时，表示该用户为合法用户，提示登录成功信息，否则提示用户名或密码有误的错误信息。

该程序的运行结果下所示：

```
------欢迎使用大数据管理平台------
用户名：
adinm
密码：
admin
用户名或密码有误！
```

```
------欢迎使用大数据管理平台------
用户名：
admin
密码：
admin
登录成功！
```

## getClass() 方法

getClass() 方法返回对象所属的类，是一个 Class 对象。通过 Class 对象可以获取该类的各种信息，包括类名、父类以及它所实现接口的名字等。

#### 例 2

编写一个实例，演示如何对 String 类型调用 getClass() 方法，然后输出其父类及实现的接口信息。具体实现代码如下：

```
public class Test02
{
    public static void printClassinfo(Object obj)
    {
        //获取类名
        System.out.println("类名："+obj.getClass().getName());

        //获取父类名
        System.out.println("父类："+obj.getClass().getSuperclass().getName());
        System.out.println("实现的接口有：");

        //获取实现的接口并输出
        for(int i=0;i<obj.getClass().getInterfaces().length;i++)
        {
            System.out.println(obj.getClass().getInterfaces()[i]);
        }
    }
    public static void main(String[] args)
    {
        String strObj=new String();
        printClassInfo(strObj);
    }
}
```

该程序的运行结果如下：

```
类名：java.lang.String
父类：java.lang.Object
实现的接口有：
interface java.io.Serializable
interface java.lang.Comparable
interface java.lang.CharSequence
```