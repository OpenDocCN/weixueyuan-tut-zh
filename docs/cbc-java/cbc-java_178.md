# Java sleep 方法的作用（sleep()）

sleep() 方法的作用是在指定的毫秒数内让当前“正在执行的线程”休眠（暂停执行）。这个“正在执行的线程”是指 this.currentThread() 返回的线程。

#### 例 1

下面通过一个案例来理解使用 sleep() 方法判断线程是否活动的标准。假设 MyThread10 线程类的代码如下：

```
package ch14;
public class MyThread10 extends Thread
{
    @Override 
    public void run()
    { 
        try
        { 
            System.out.println("正在运行的线程名称："+this.currentThread().getName()+" 开始"); 
            Thread.sleep(2000);    //延时 2 秒
            System.out.println("正在运行的线程名称："+this.currentThread().getName()+" 结束"); 
        }
        catch(InterruptedException e)
        { 
            e.printStackTrace(); 
        } 
    }
}
```

下面编写启动 MyThread10 线程的代码，具体实现如下：

```
package ch14;
public class Test14
{
    public static void main(String[] args)
    {
        MyThread11 mythread=new MyThread11();
        System.out.println("主线程开始时间="+System.currentTimeMillis());
        mythread.start();
        System.out.println("主线程结束时间="+System.currentTimeMillis());
    }
}
```

如上述代码所示，主线程创建一个 MyThread10 线程实例之后直接调用 run() 方法启动线程，整个过程都在主线程中完成。程序运行后的输出结果如下所示。

```
主线程开始时间=1540963362783
主线程结束时间=1540963362783
正在运行的线程名称：Thread-0 开始
正在运行的线程名称：Thread-0 结束
```

#### 例 2

在上个案例中，子线程中的延时导致主线程也进行了延时。下面再看一个 sleep() 方法应用的案例，这里使用的是 MyThread11 线程类，该类代码如下：

```
package ch14;
public class MyThread11 extends Thread
{
    @Override
    public void run()
    {
        try
        {
            System.out.println("正在运行的线程名称："+this.currentThread().getName()+" 开始时间="+System.currentTimeMillis());
            Thread.sleep(2000);    //延时 2 秒
            System.out.println("正在运行的线程名称："+this.currentThread().getName()+" 结束时间="+System.currentTimeMillis());
        }
        catch(InterruptedException e)
        {
            e.printStackTrace();
        }
    }
}
```

下面编写主线程的代码，在这里使用 start() 方法来启动 MyThread11 线程。具体代码如下：

```
package ch14;
public class Test14
{
    public static void main(String[] args)
    {
        MyThread11 mythread=new MyThread11();
        System.out.println("主线程开始时间="+System.currentTimeMillis());
        mythread.start();
        System.out.println("主线程结束时间="+System.currentTimeMillis());
    }
}
```

此时运行程序将看到如下所示运行效果。

```
主线程开始时间=1540964257366
主线程结束时间=1540964257366
正在运行的线程名称：Thread-0 开始时间=1540964257366
正在运行的线程名称：Thread-0 结束时间=1540964259366
```

由于 main 线程与 MyThread11 线程是异步执行的，所以首先输出主线程的信息。而 MyThread11 线程是随后运行的，在最后两行输出它的运行时间信息。