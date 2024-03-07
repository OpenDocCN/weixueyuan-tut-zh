# Java 非线程安全问题的解决方法

在上节《多线程之间访问实例变量》中出现了一个术语——非线程安全。非线程安全主要是指多个线程对同一个对象中的同一个实例变量进行操作时会出现值被更改、值不同步的情况，进而影响程序的执行流程。下面用一个示例来学习一下如何解决非线程安全问题。

本案例模拟了多线程下的用户登录验证功能。首先编写一个类实现验证功能， LoginCheck 类的代码如下：

```
package ch14;
public class LoginCheck
{
    private static String username; 
    private static String password; 
    public static void doPost(String _username,String _password)
    { 
        try
        { 
            username=_username; 
            if (username.equals("admin"))
            { 
                Thread.sleep(5000); 
            } 
            password=_password; 
            System.out.println("username="+username+"password="+password); 
        }
        catch(InterruptedException e)
        { 
            // TODO Auto-generated catch block 
            e.printStackTrace(); 
        } 
    } 
```

}

接下来创建线程类 LoginThreadA 和 LoginThreadB，这两个线程都调用 LoginCheck 类进行登录信息。其中 LoginThreadA 类的代码如下：

```
package ch14;
public class LoginThreadA extends Thread
{
    public void run()
    { 
        LoginCheck.doPost("admin","admin"); 
    }
}
```

LoginThreadB 类的代码如下：

```
package ch14;
public class LoginThreadB extends Thread
{ 
    public void run()
    { 
        LoginCheck.doPost("root","root"); 
    }
}
```

现在编写主线程程序，分别创建 LoginThreadA 线程实例和 LoginThreadB 线程实现，然后启动这两个线程。主线程的代码如下：

```
package ch14;
public class Test07
{
    public static void main(String[] args)
    {
        LoginThreadA a=new LoginThreadA();
        a.run();    //启动线程 LoginThreadA
        LoginThreadB b=new LoginThreadB();
        b.run();    //启动线程 LoginThreadB
    }
}
```

程序运行后的结果如下所示：

```
username=root password=admin
username=root password=root
```

从运行结果中可以看到用户名 root 出现了这两次，这是由于多个线程同时修改 username，导致值不一致的情况。

仔细查看代码可以发现问题出现在两个线程都会调用 doPost() 方法上。解决这个非线程安全问题的方法是使用 synchronized 关键字修饰 doPost() 方法，即不允许多个线程同时修改 doPost() 方法中的变量。更改代码如下：

```
package ch14;
public class LoginCheck
{
    private static String username; 
    private static String password; 
    synchronized public static void doPost(String _username,String _password)
    { 
        try
        { 
            username=_username; 
            if (username.equals("admin"))
            { 
                Thread.sleep(5000); 
            } 
            password=_password; 
            System.out.println("username="+username+"password="+password); 
        }
        catch (InterruptedException e)
        { 
            // TODO Auto-generated catch block 
            e.printStackTrace(); 
        } 
    } 
}
```

再次运行主线程，此时将看到如下所示的结果，说明不存在“非线程安全”问题了。

```
username=admin password=admin
username=root password=root
```