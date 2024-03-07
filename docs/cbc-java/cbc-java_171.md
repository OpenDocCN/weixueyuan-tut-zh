# Java 多线程的实现方式

在 Java 的 JDK 开发包中，已经自带了对多线程技术的支持，可以方便地进行多线程编程。实现多线程编程的方式主要有两种：一种是继承 Thread 类，另一种是实现 Runnable 接口。下面详细介绍这两种具体实现方式。

## 继承 Thread 类

在学习如何实现多线程前，先来看看 Thread 类的结构，如下：

```
public class Thread implements Runnable
```

从上面的源代码可以发现，Thread 类实现了 Runnable 接口，它们之间具有多态关系。

其实，使用继承 Thread 类的方式实现多线程，最大的局限就是不支持多继承，因为 Java 语言的特点就是单根继承，所以为了支持多继承，完全可以实现 Runnable 接口的方式，一边实现一边继承。但用这两种方式创建的线程在工作时的性质是一样的，没有本质的区别。

Thread 类有如下两个常用构造方法：

1.  `public Thread(String threadName)`
2.  `public Thread()`

继承 Thread 类实现线程的语法格式如下：

```
public class NewThreadName extends Thread
{    //NewThreadName 类继承自 Thread 类
    public void run()
    {
        //线程的执行代码在这里
    }
}
```

线程实现的业务代码需要放到 run() 方法中。当一个类继承 Thread 类后，就可以在该类中覆盖 run() 方法，将实现线程功能的代码写入 run() 方法中，然后同时调用 Thread 类的 start() 方法执行线程，也就是调用 run() 方法。

Thread 对象需要一个任务来执行，任务是指线程在启动时执行的工作，该工作的功能代码被写在 run() 方法中。当执行一个线程程序时，就会自动产生一个线程，主方法正是在这个线程上运行的。当不再启动其他线程时，该程序就为单线程程序。主方法线程启动由 Java 虚拟机负责，开发人员负责启动自己的线程。

如下代码演示了如何启动一个线程：

```
new NewThreadName().start();    //NewThreadName 为继承自 Thread 的子类
```

注意：如果 start() 方法调用一个已经启动的线程，系统将会抛出 IllegalThreadStateException 异常。

#### 例 1

编写一个 Java 程序演示线程的基本使用方法。这里创建的自定义线程类为 MyThread，此类继承自 Thread，并在重写的 run() 中输出一行字符串。

MyThread 类代码如下：

```
public class MyThread extends Thread
{
    @Override
    public void run()
    {
        super.run();
        System.out.println("这是线程类 MyThread");
    }
}
```

接下来编写启动 MyThread 线程的主方法，代码如下：

```
public static void main(String[] args)
{
    MyThread mythread=new MyThread();    //创建一个线程类
    mythread.start();    //开启线程
    System.out.println("运行结束！");    //在主线程中输出一个字符串
}
```

运行上面的程序将看到如下所示的运行效果。

```
运行结束！
这是线程类 MyThread
```

从上面的运行结果来看，MyThread 类中 run() 方法执行的时间要比主线程晚。这也说明在使用多线程技术时，代码的运行结果与代码执行顺序或调用顺序是无关的。同时也验证了线程是一个子任务，CPU 以不确定的方式，或者说以随机的时间来调用线程中的 run() 方法，所以就会出现先打印“运行结束！”，后输出“这是线程类 MyThread”这样的结果了。

#### 例 2

上面介绍了线程的调用具有随机性，为了更好地理解这种随机性这里编写了一个案例进行演示。

(1) 首先创建自定义的线程类 MyThread01，代码如下：

```
package ch14;
public class MyThread01 extends Thread
{
    @Override 
    public void run()
    { 
        try
        { 
            for(int i=0;i<10;i++)
            { 
                int time=(int)(Math.random()*1000); 
                Thread.sleep(time); 
                System.out.println("当前线程名称="+Thread.currentThread().getName()); 
            } 
        }
        catch(InterruptedException e)
        { 
            e.printStackTrace(); 
        } 
    } 
}
```

(2) 接下来编写主线程代码，在这里除了启动上面的 MyThread01 线程外，还实现了 MyThread01 线程相同的功能。主线程的代码如下：

```
package ch14;
public class Test02
{
    public static void main(String[] args)
    { 
        try
        { 
            MyThread01 thread=new MyThread01(); 
            thread.setName("myThread"); 
            thread.start(); 
            for (int i=0;i<10;i++)
            { 
                int time=(int)(Math.random()*1000); 
                Thread.sleep(time); 
                System.out.println("主线程名称="+Thread.currentThread().getName()); 
            } 
        }
        catch(InterruptedException e)
        { 
            e.printStackTrace(); 
        }
    }
}
```

在上述代码中，为了展现出线程具有随机特性，所以使用随机数的形式来使线程得到挂起的效果，从而表现出 CPU 执行哪个线程具有不确定性。

MyThread01 类中的 start() 方法通知“线程规划器”此线程已经准备就绪，等待调用线程对象的 run() 方法。这个过程其实就是让系统安排一个时间来调用 Thread 中的 run() 方法，也就是使线程得到运行，启动线程，具有异步执行的效果。

如果调用代码 thread.run() 就不是异步执行了，而是同步，那么此线程对象并不交给“线程规划器”来进行处理，而是由 main 主线程来调用 run() 方法，也就是必须等 run() 方法中的代码执行完后才可以执行后面的代码。

这种采用随机数延时调用线程的方法又称为异步调用，程序运行的效果如下所示。

```
当前线程名称=myThread
主线程名称=main
当前线程名称=myThread
当前线程名称=myThread
当前线程名称=myThread
主线程名称=main
当前线程名称=myThread
当前线程名称=myThread
主线程名称=main
当前线程名称=myThread
主线程名称=main
当前线程名称=myThread
当前线程名称=myThread
当前线程名称=myThread
主线程名称=main
主线程名称=main
主线程名称=main
主线程名称=main
主线程名称=main
主线程名称=main
```

#### 例 3

除了异步调用之外，同步执行线程 start() 方法的顺序不代表线程启动的顺序。下面创建一个案例演示同步线程的调用。

(1) 首先创建自定义的线程类 MyThread02，代码如下：

```
package ch14;
public class MyThread02 extends Thread
{
    private int i; 
    public MyThread02(int i)
    { 
        super(); 
        this.i=i; 
    } 
    @Override 
    public void run()
    { 
        System.out.println("当前数字："+i); 
    }
}
```

(2) 接下来编写主线程代码，在这里创建 10 个线程类 MyThread02，并按顺序依次调用它们的 start() 方法。主线程的代码如下：

```
package ch14;
public class Test03
{
    public static void main(String[] args)
    { 
        MyThread02 t11=new MyThread02(1); 
        MyThread02 t12=new MyThread02(2); 
        MyThread02 t13=new MyThread02(3); 
        MyThread02 t14=new MyThread02(4); 
        MyThread02 t15=new MyThread02(5); 
        MyThread02 t16=new MyThread02(6); 
        MyThread02 t17=new MyThread02(7); 
        MyThread02 t18=new MyThread02(8); 
        MyThread02 t19=new MyThread02(9); 
        MyThread02 t110=new MyThread02(10); 
        t11.start(); 
        t12.start(); 
        t13.start(); 
        t14.start(); 
        t15.start(); 
        t16.start(); 
        t17.start(); 
        t18.start(); 
        t19.start(); 
        t110.start(); 
    }
}
```

程序运行后的结果如下所示，从运行结果中可以看到，虽然调用时数字是有序的，但是由于线程执行的随机性，导致输出的数字是无序的，而且每次顺序都不一样。

```
当前数字：1
当前数字：3
当前数字：5
当前数字：7
当前数字：6
当前数字：2
当前数字：4
当前数字：8
当前数字：10
当前数字：9
```

## 实现 Runnable 接口

如果要创建的线程类已经有一个父类，这时就不能再继承 Thread 类，因为 Java 不支持多继承，所以需要实现 Runnable 接口来应对这样的情况。

实现 Runnable 接口的语法格式如下：

```
public class thread extends Object implements Runnable
```

提示：从 JDK 的 API 中可以发现，实质上 Thread 类实现了 Runnable 接口，其中的 run() 方法正是对 Runnable 接口中 run() 方法的具体实现。

实现 Runnable 接口的程序会创建一个 Thread 对象，并将 Runnable 对象与 Thread 对象相关联。Thread 类有如下两个与 Runnable 有关的构造方法：

1.  `public Thread(Runnable r);`
2.  `public Thread(Runnable r,String name);`

使用上述两种构造方法之一均可以将 Runnable 对象与 Thread 实例相关联。使用 Runnable 接口启动线程的基本步骤如下。

1.  创建一个 Runnable 对象。
2.  使用参数带 Runnable 对象的构造方法创建 Thread 实例。
3.  调用 start() 方法启动线程。

通过实现 Runnable 接口创建线程时开发人员首先需要编写一个实现 Runnable 接口的类，然后实例化该类的对象，这样就创建了 Runnable 对象。接下来使用相应的构造方法创建 Thread 实例。最后使用该实例调用 Thread 类的 start() 方法启动线程，如图 1 所示。

![](img/58a65c8302d122c90185759ce79e16ae.jpg)
图 1 使用 Runnable 接口启动线程流程

#### 例 4

编写一个简单的案例演示如何实现 Runnable 接口，以及如何启动线程。

(1) 首先创建一个自定义的 MyRmmable 类，让该类实现 Runnable 接口，并在 run() 方法中输出一个字符串。代码如下：

```
package ch14;
public class MyRunnable implements Runnable
{
    @Override 
    public void run()
    { 
        System.out.println("MyRunnable 运行中!"); 
    }
}
```

(2) 接下来在主线程中编写代码，创建一个 MyRunnable 类实例，并将该实例作为参数传递给 Thread 类的构造方法，最后调用 Thread 类的 start() 方法启动线程。具体实现代码如下：

```
package ch14;
public class Test04
{
    public static void main(String[] args)
    {
        Runnable runnable=new MyRunnable();
        Thread thread=new Thread(runnable);
        thread.start();
        System.out.println("主线程运行结束!");
    }
}
```

如上述代码所示，启动线程的方法非常简单。运行结果如下所示，同样验证了线程执行的随机性。

```
主线程运行结束!
MyRunnable 运行中!
```

注意：要启动一个新的线程，不是直接调用 Thread  子类对象的 run() 方法，而是调用 Thread 子类的 start() 方法。Thread 类的 start() 方法会产生一个新的线程，该线程用于执行 Thread 子类的 run() 方法。

## 两种方法的比较

虽然 Thread 类和 Runnable 接口都可以创建线程，但是它们也都有各自的优缺点。

#### 1\. 继承 Thread 类的优缺点

当一个 run() 方法体现在继承 Thread 类中时，可以用 this 指向实际控制运行的 Thread 实例。因此，代码不需要使用以下控制语句：

```
Thread.currenThread().sleep();
```

不再使用上面的控制语句，而是可以简单地使用 Threadsleep() 方法，继承 Thread 类的方式使代码变得简单易读。

#### 2\. 实现 Runnable 接口的优缺点

从面向对象的角度来看，Thread 类是一个虚拟处理机严格的封装，因此只有当处理机模型修改或扩展时，才应该继承该类。由于 Java 技术只允许单一继承，因此如果已经继承了 Thread 类，就不能再继承其他任何类，这会使用户只能采用实现 Runnable 接口的方式创建线程。