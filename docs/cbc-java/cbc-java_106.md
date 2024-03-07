# Java 用户修改密码

每一个应用系统都离不开用户模块。用户模块除了提供登录之外，还允许用户查看自己的信息和进行修改。本实例将创建一个用户类，然后再创建一个测试类调用用户类，实现修改密码的功能。

本实例的用户类非常简单，仅包含用户名和密码两个属性。

```
public class Member
{
    //用户类
    private String username;    //用户名
    private String password;    //密码
    public String getUsername()
    {
        return username;
    }
    public void setUsername(String username)
    {
        this.username=username;
    }
    public String getPassword()
    {
        return password;
    }
    public void setPassword(String password)
    {
        this.password=password;
    }
    public Member(String username,String password)
    {
        this.username=username;
        this.password=password;
    }
    public String toString()
    {
        //输出用户信息
        return"用户名："+username+"\n 密码："+password;
    }
}
```

创建 MemberTest 类实现修改密码的功能，即当用户输入的密码与原来密码相同时方可进行修改密码操作，否则提示用户输入的密码不正确。具体的实现代码如下：

```
import java.util.Scanner;
public class MemberTest
{
    public static void main(String[] args)
    {
        Member admin=new Member("admin","123456");    //创建用户对象
        Scanner input=new Scanner(System.in);
        System.out.println("请输入原密码：");
        String pwd=input.next();    //获取用户输入的原密码
        if(pwd.equais(admin.getPassword()))
        {    //对用户输入的密码进行验证
            System.out.prlntln("请输入新密码：");
            admin.setPassword(input.next());    //获取用户输入的新密码
        }
        else
        {
            System.out.println("输入的密码错误，无法进行修改！");
        }
        System.out.println("----------------用户信息----------------\n"+admin);
    }
}
```

如上述代码，在 main() 方法中创建了 Member 类的对象 admin，并访问了该对象的成员变量 password 和成员方法 toString()。

运行该程序。当用户输入的原密码正确时，则可以继续向控制台输入新的密码，并将输入的新密码赋值给 Member 类的 password 属性，从而输出更新后的用户信息，如下所示。

```
请输入原密码：
123456
请输入新密码：
111111
----------------用户信息----------------
用户名：admin
密码：111111
```

当用户输入的原密码错误时，则提示无法进行更新操作信息，如下所示。

```
请输入原密码：
123456789
输入的密码错误，无法进行修改！
----------------用户信息----------------
用户名：admin
密码：123456
```