# Java 字符串的比较（equals()、equalsIgnoreCase()和 compareTo()）

在 Java 中，比较字符串的常用方法有 3 个：equals() 方法、equalsIgnoreCase() 方法、 compareTo() 方法。下面详细介绍这 3 个方法的使用。

## equals() 方法

equals() 方法将逐个地比较两个字符串的每个字符是否相同。对于字符的大小写，也在检查的范围之内。equals() 方法的语法格式如下：

```
字符串 1.equals(字符串 2);
```

例如：

```
String str1="abc";
String str2=new String("abc");
String str3="ABC";
System.out.println(str1.equals(str2));    //输出 true
System.out.println(str1.equals(str3));    //输出 false
```

#### 例 1

在第一次进入系统时要求管理员设置一个密码，出于安全考虑密码需要输入两次，如果两次输入的密码一致才生效，否则提示失败。具体实现代码如下：

```
import java.utii.Scanner; public class Testl2
{
    public static void main(String[] args)
    {
        String sys="学生信息管理";
        System.out.println("欢迎进入《"+sys+"》系统");
        System.out.println("请设置一个管理员密码：");
        Scanner input=new Scanner(System.in);
        String pass=input.next();    //设置密码
        System.out.println("重复管理员密码：");
        input=new Scanner(System.in);
        String pass1=input.next();    //确认密码
        if(pass.equals(pass1))
        {   //比较两个密码
            System.out.println("已生效，请牢记密码："+ pass);
        }
        else
        {
            System.out.println("两次密码不一致，请重新设置。");
        }
    }
}
```

运行该程序，由于 equals() 方法区分大小写，所以当两次输入的密码完全一致时，equals() 方法返回 true，输出结果如下所示：

```
欢迎进入《学生信息管理》系统
请设置一个管理员密码：
abcdef
重复管理员密码：
abcdef
已生效，请牢记密码：abcdef
```

否则输出如图下所示的结果：

```
欢迎进入《学生信息管理》系统
请设置一个管理员密码：
abcdef
重复管理员密码：
aBcdef
两次密码不一致，请重新设置。
```

## equalsIgnoreCase() 方法

equalsIgnoreCase() 方法的作用和语法与 equals() 方法完全相同，唯一不同的是 equalsIgnoreCase() 比较时不区分大小写。例如：

```
String str1="abc";
String str2="ABC";
System.out.println(str1.equalsIgnoreCase(str2));    //输出 true
```

#### 例 2

在会员系统中需要输入用户名和密码进行检验，下面使用 equalsIgnoreCase() 方法实现检验登录时不区分用户名和密码的大小写，具体的代码实现如下所示。

```
public static void main(String[] args)
{
    String sys="学生信息管理";
    System.out.println("欢迎进入《"+sys+"》系统");
    System.out.println("请输入管理员名称：");
    Scanner input=new Scanner(System.in);
    String name=input.next();    //获取用户输入的名称
    System.out.println("请输入管理员密码：");
    input=new Scanner(System.in);
    String pass=input.next();    //获取用户输入的密码
    //比较用户名与密码，注意此处忽略大小写
    if(name.equalsIgnoreCase("admin")&&pass.equalsIgnoreCase("somboy"))
    {   //验证
        System.out.println("登录成功。");
    }
    else
    {
        System.out.println("登录失败。");
    }
}
```

在上述代码中，由于使用 equalsIgnoreCase() 方法进行比较，所以会忽略大小写判断。因此输入 ADMIN 和 SOMBOY 也会验证通过，如下所示：

```
欢迎进入《学生信息管理》系统
请输入管理员名称：
ADMIN
请输入管理员密码：
SOMBOY
登录成功。
```

否则输出结果如下所示：

```
欢迎进入《学生信息管理》系统
请输入管理员名称：
admin
请输入管理员密码：
sommboy
登录失败。
```

## compareTo() 方法

compareTo() 方法用于按字典顺序比较两个字符串的大小，该比较是基于字符串各个字符的 Unicode 值。compareTo() 方法的语法格式如下：

```
str.compareTo(String otherstr);
```

它会按字典顺序将 str 表示的字符序列与 otherstr 参数表示的字符序列进行比较。如果按字典顺序 str 位于 otherster 参数之前，比较结果为一个负整数；如果 str 位于 otherstr 之后，比较结果为一个正整数；如果两个字符串相等，则结果为 0。

提示：如果两个字符串调用 equals() 方法返回 true，那么调用 compareTo() 方法会返回 0。

#### 例 3

编写一个简单的 Java 程序，演示 compareTo() 方法比较字符串的用法，以及返回值的区别。代码如下：

```
public static void main(String[] args)
{
    String str="A";
    String str1="a";
    System.out.println("str="+str);
    System.out.println("str1="+str1);
    System.out.println("str.compareTo(str1)的结果是："+str.compareTo(str1));
    System.out.println("str1.compareTo(str)的结果是："+str1.compareTo(str));
    System.out.println("str1.compareTo('a')的结果是："+str1.compareTo("a"));
}
```

上述代码定义了两个字符串"A"和"a"， 然后调用 compareTo() 方法进行相互比较。最后一行代码拿"a"与"a"进行比较，由于两个字符串相同比较结果为 0。运行后的输出结果如下：

```
str=A
str1=a
str.compareTo(str1)的结果是：-32
str1.compareTo(str)的结果是：32
str1.compareTo('a')的结果是：0
```