# Java 验证用户名和密码

假设在某仓库管理系统的登录界面中需要输入用户名和密码，其中用户名只能由 6~10 位数字组成，密码只能有 6 位，任何不符合用户名或者密码要求的情况都视为异常，并且需要捕获并处理该异常。

下面使用自定义异常类来完成对用户登录信息的验证功能，实现步骤如下。

(1) 编写自定义异常类 LoginException，该类继承自 Exception。在 LoginException 类中包含两个构造方法，分别为无参的构造方法和含有一个参数的构造方法，代码如下：

```
public class LoginException extends Exception
{
    public LoginException()
    {
        super();
    }
    public LoginException(String msg)
    {
        super(msg);
    }
}
```

(2) 创建测试类 Test08，在该类中定义 validateLogin() 方法，用于对用户名和密码进行验证。当用户名或者密码不符合要求时，使用自定义异常类 LoginException 输出相应的异常信息。validateLogin() 方法的定义如下：

```
public boolean validateLogin(String username,String pwd)
{
    boolean con=false;    //用户名和密码是否正确
    boolean conUname=false;    //用户名格式是否正确
    try
    {
        if(username.length()>=6&&username.length()<=10)
        {
            for(int i=0;i<username.length();i++)
            {
                char ch=username.charAt(i);    //获取每一个字符
                if(ch>='0'&&ch<='9')
                {    //判断字符是否为 0~9 的数字
                    conUname=true;    //设置 conUname 变量值为 true
                }
                else
                {    //如果字符不是 0~9 的数字，则拋出 LoginException 异常
                    conUname=false;
                    throw new LoginException("用户名中包含有非数字的字符！");
                }
            }
        }
        else
        {    //如果用户名长度不在 6~10 位之间，拋出异常
            throw new LoginException("用户名长度必须在 6〜10 位之间！");
        }
        if(conUname)
        {    //如果用户名格式正确，判断密码长度
            if(pwd.length()==6)
            {    //如果密码长度等于 6
                con=true;    //设置 con 变量的值为 true，表示登录信息符合要求
            }
            else
            {    //如果密码长度不等于 6，拋出异常
                con=false;
                throw new LoginException("密码长度必须为 6 位！");
            }
        }
    }
    catch(LoginException e)
    {    //捕获 LoginException 异常
        System.out.println(e.getMessage());
    }
    return con;
}
```

(3) 在 Test08 类中添加 main() 方法，调用 validateLogin() 方法，如果该方法返回 true，则输出登录成功的信息。main() 方法的定义如下：

```
public static void main(String[] args)
{
    Scanner input=new Scanner(System.in);
    System.out.println("用户名：");
    String username=input.next();
    System.out.println("密码：");
    String password=input.next();
    Test08 lt=new Test08 ();
    boolean con=lt.validateLogin(username,password);    //调用 validateLoginO 方法
    if(con)
    {
        System.out.println("登录成功！");
    }
}
```

在本程序的 validateLogin() 方法中使用条件控制语句和  for 循环语句分别对用户名和密码进行了验证。任何不符合用户名或者密码要求的情况都拋出自定义异常 LoginException，并在 catch 语句中捕获该异常，输出异常信息。

运行程序，当用户输入的用户名含有非数字字符时将拋出 LoginException 异常，执行 catch 语句块中的代码打印异常信息，如下所示。

```
用户名：
xiake8!
密码：
123456
用户名中包含有非数字的字符！
```

当用户输入的用户名长度不在 6~10 位时同样会拋出 LoginException 异常并打印异常信息，如下所示。

```
用户名：
administrator
密码：
123456
用户名长度必须在 6~10 位之间！
```

当用户输入的登录密码不等于 6 位时也会拋出 LogWException 异常，并打印出异常信息，如下所示。

```
用户名：
20181024
密码：
12345
密码长度必须为 6 位！
```

当用户输入的用户名和密码都符合要求时，则打印登录成功的信息，如下所示。

```
用户名：
20181024
密码：
123456
登录成功！
```