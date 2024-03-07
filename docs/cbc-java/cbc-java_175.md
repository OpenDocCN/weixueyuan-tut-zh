# Java 多线程的同步机制：synchronized

如果程序是单线程的，就不必担心此线程在执行时被其他线程“打扰”，就像在现实世界中，在一段时间内如果只能完成一件事情，不用担心做这件事情被其他事情打扰。但是，如果程序中同时使用多线程，好比现实中的“两个人同时通过一扇门”，这时就需要控制，否则容易引起阻塞。

为了处理这种共享资源竞争，可以使用同步机制。所谓同步机制，指的是两个线程同时作用在一个对象上，应该保持对象数据的统一性和整体性。Java 提供 synchronized 关键字，为防止资源冲突提供了内置支持。共享资源一般是文件、输入/输出端口或打印机。

细心的读者可以发现，在《多线程之间访问实例变量》和《非线程安全问题的解决方法》中都已经使用到了 synchronized 关键字。

在一个类中，用 synchronized 关键字声明的方法为同步方法。格式如下：

```
class 类名
{
    public synchronized 类型名称 方法名称()
    {
        //代码
    }
}
```

Java 有一个专门负责管理线程对象中同步方法访问的工具——同步模型监视器，它的原理是为每个具有同步代码的对象准备唯一的一把“锁”。当多个线程访问对象时，只有取得锁的线程才能进入同步方法，其他访问共享对象的线程停留在对象中等待。

synchronized 不仅可以用到同步方法，也可以用到同步块。对于同步块，synchronized 获取的是参数中的对象锁。格式如下：

```
synchronized(obj)
{
    //代码
}
```

当线程执行到这里的同步块时，它必须获取 obj 这个对象的锁才能执行同步块，否则线程只能等待获得锁。必须注意的是，Obj 对象的作用范围不同，控制情况也不尽相同。如下代码为简单的一种使用：

```
public void method()
{
    Object obj=new Object();
    synchronized(obj)
    {
        //代码
    }
}
```

上述代码创建局部对象 Obj，由于每一个线程执行到 Object obj=new Object() 时都会产生一个 obj 对象，每一个线程都可以获得新创建的 obj 对象的锁而不会相互影响，因此这段程序不会起到同步作用。如果同步的是类的属性，情况就不同了。

#### 例 1

在前面几节中，使用了 synchronized 关键字同步方法来解决非线程安全的问题。下面通过一个案例演示 println() 方法与 i-- 联合使用时“有可能”出现的另外一种异常情况，并说明其中的原因。

(1) 首先创建线程类 MyThread05，该类的代码很简单，如下所示：

```
package ch14;
public class MyThread05 extends Thread
{
    private int i=5;
    @Override
    public void run()
    {
        System.out.println("当前线程名称="+Thread.currentThread().getName()+",i="+(i--));
        //注意：代码 i--由前面项目中单独一行运行改成在当前项目中在 println()方法中直接进行打印
    }
}
```

(2) 编写主线程代码，首先创建一个 MyThread05 线程类，再启动 5 个相同的线程。具体代码如下：

```
package ch14;
public class Test08
{
    public static void main(String[] args)
    {
        MyThread05 run=new MyThread05(); 
        Thread t1=new Thread(run); 
        Thread t2=new Thread(run); 
        Thread t3=new Thread(run); 
        Thread t4=new Thread(run); 
        Thread t5=new Thread(run); 
        t1.start(); 
        t2.start(); 
        t3.start(); 
        t4.start(); 
        t5.start();
    }
```

}

从如下所示的运行效果可以看出，i 的值并不是从 5 递减 1。这是因为虽然 println() 方法在内部是同步的，但 i-- 操作却是在进入 println() 之前发生的，所以有发生非线程安全问题的概率。

```
当前线程名称=Thread-2,i=5
当前线程名称=Thread-3,i=2
当前线程名称=Thread-4,i=3
当前线程名称=Thread-1,i=4
当前线程名称=Thread-5,i=1
```

(3) 为了防止发生非线程安全问题，应继续使用同步方法。在这里使用同步块完成，修改后的代码如下：

```
package ch14;
public class MyThread05 extends Thread
{
    private int i=5;
    @Override
    public void run()
    {
        synchronized (this)
        {
            System.out.println("当前线程名称="+Thread.currentThread().getName()+",i="+(i--));
            //注意：代码 i--由前面项目中单独一行运行改成在当前项目中在 println()方法中直接进行打印
        }
    }
}
```

(4) 再次运行将看到如下所示的正常的运行效果。

```
当前线程名称=Thread-1,i=5
当前线程名称=Thread-2,i=4
当前线程名称=Thread-3,i=3
当前线程名称=Thread-4,i=2
当前线程名称=Thread-5,i=1
```