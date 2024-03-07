# Java curentThread()方法的作用

curentThread() 方法可返回代码段正在被哪个线程调用的信息。下面通过一个示例进行说明。

#### 例 1

假设在主线程中有如下代码：

```
public class Run1
{
    public static void main(String[] args)
    {
        //调用 currentThread()方法输出当前线程名称
        System.out.println(Thread.currentThread().getName());
    }
}
```

执行后在控制台会输出“main”，这说明 main() 方法被名为 main 的线程调用。

上例中 currentThread() 方法在主线程中，再来看一个在线程类中调用该方法的示例。假设 MyThread06 线程类的代码如下：

```
package ch14;
public class MyThread06 extends Thread
{
    public MyThread06()
    { 
        //调用 currentThread()方法输出当前线程名称
        System.out.println("构造方法的打印："+Thread.currentThread().getName()); 
     } 
     @Override 
     public void run()
     { 
        //调用 currentThread()方法输出当前线程名称
        System.out.println("run 方法的打印："+Thread.currentThread().getName()); 
     } 
}
```

接下来编写主线程代码，创建 MyThread06 线程实例并启动线程。代码如下：

```
package ch14;
public class Test09
{
    public static void main(String[] args)
    {
        MyThread06 mythread=new MyThread06(); 
        mythread.start(); 
        //mythread.run();
    }
}
```

运行主线程将看到如下所示的结果。

```
构造方法的打印：main
run 方法的打印：Thread-0
```

从运行结果可以发现，MyThread06 类的构造函数是被 main 线程调用的，而 run() 方法是被名称为 Thread-0 的线程调用的，run() 方法是自动调用的方法。

对主线程的代码进行简单修改，使用“mythread.run()”代码来启动线程，此时的运行结果如下所示。

```
构造方法的打印：main
run 方法的打印：main
```

和之前的运行结果进行对比可以发现，此时 run() 方法的打印也是 main 线程调用的。

#### 例 2

再来测试一个更复杂的情况。假设 MyThread07 线程类的代码如下：

```
package ch14;
public class MyThread07 extends Thread
{
    public MyThread07()
    { 
        System.out.println("构造方法---开始"); 
        System.out.println("Thread.currentThread().getName()="+Thread.currentThread().getName()); 
        System.out.println("this.getName()="+this.getName()); 
        System.out.println("构造方法---结束"); 
    } 
    @Override 
    public void run()
    { 
        System.out.println("run()方法---开始"); 
        System.out.println("Thread.currentThread().getName()="+Thread.currentThread().getName()); 
        System.out.println("this.getName()="+this.getName()); 
        System.out.println("run()方法---结束"); 
    } 
```

}

如上述代码所示，在 MyThread07 类的构造方法和 run() 方法中都调用了 currentThread() 方法。那么在启动该线程时构造方法的线程名称和 run() 方法中的线程名称是否相同呢？带着上面的疑问，我们来编写一个主线程对 MyThread07 类进行测试。主线程代码如下：

```
package ch14;
public class Test10
{
    public static void main(String[] args)
    {
        //创建 MyThread07 线程实例
        MyThread07 myThread=new MyThread07();
        //创建一个线程
        Thread t1=new Thread(myThread);
        //设置线程的名称
        t1.setName("A");
        //启动线程
        t1.start();
    }
```

}

执行后的输出结果如下：

```
构造方法---开始
Thread.currentThread().getName()=main
this.getName()=Thread-0
构造方法---结束
run()方法---开始
Thread.currentThread().getName()=A
this.getName()=Thread-0
run()方法---结束
```

从结果可以发现，虽然 this.getName() 方法返回的都是 Thread-0，但是在构造方法中使用的即是 main 线程，而在 run() 方法中使用的是 A 线程。