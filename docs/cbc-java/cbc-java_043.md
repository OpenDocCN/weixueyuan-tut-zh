# Java 获取字符串长度（length()）

在 Java 中，要获取字符串的长度，可以使用 String 类的 length() 方法，其语法形式如下：

```
字符串名.length();
```

#### 例 1

在学生信息管理系统中对管理员密码有这样的规定，即密码长度必须大于 6 位且小于 12 位。因为密码太短容易被破解，太长的话又不容易记住。这就需要首先获取用户输入的密码字符串，然后调用 length() 方法获取长度，再做进一 步的长度判断，最终实现代码如下所示：

```
import java.util.Scanner;
public class Test05
{
    public static void main(String[] args)
    {
        String sys="学生信息管理";//字义一个字符串表示系统名称
        System.out.println("欢迎进入《"+sys+"》系统");//输出系统名称
        System.out.println("请设置一个管理员密码：");
        Scanner input=new Scanner(System.in);
        String pass=input.next();//获取用户输入的密码
        int length=pass.length();//获取密码的长度
        if(length>6&&length<12)
        {
            System.out.println("密码长度符合规定。");
            System.out.println("已生效，请牢记密码："+pass);
        }
        else if(length>=12)
            System.out.println("密码过长。");
        else
            System.out.println("密码过短。");
    }
}
```

上述代码将用户输入的密码保存到字符串变量 pass 中，再调用 pass.length() 方法将长度保存到 length 变量，然后使用 if 语句根据长度给出提示。

运行程序，当输入的密码过短时，运行结果如下所示：

```
欢迎进入《学生信息管理》系统
请设置一个管理员密码：
123456
密码过短。
```

当输入的密码符合规定时，运行结果如下所示：

```
欢迎进入《学生信息管理》系统
请设置一个管理员密码：
abc12345678
密码长度符合规定。
已生效，请牢记密码：abc12345678
```