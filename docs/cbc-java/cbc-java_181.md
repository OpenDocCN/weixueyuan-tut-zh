# Java 停止（终止）线程详解版

停止线程是在多线程开发中很重要的技术点，掌握此技术可以对线程的停止进行有效的处理。停止线程在 Java 语言中并不像 break 语句那样干脆，需要一些技巧性的处理。

使用 Java 内置支持多线程的类设计多线程应用是很常见的事情，然而，多线程给开发人员带来了一些新的挑战，如果处理不好就会导致超出预期的行为并且难以定位错误。

本节将讨论如何更好地停止一个线程。停止一个线程意味着在线程处理完任务之前停掉正在做的操作，也就是放弃当前的操作。虽然这看起来非常简单，但是必须做好防范措施，以便达到预期的效果。

停止一个线程可以使用 Threadstop() 方法，但最好不用它。虽然它确实可以停止一个正在运行的线程，但是这个方法是不安全的，而且已被弃用作废了，在将来的 Java 版本中，这个方法将不可用或不被支持。

大多数停止一个线程的操作使用 Thread.interrupt() 方法，尽管方法的名称是“停止，中止”的意思，但这个方法不会终止一个正在运行的线程，还需要加入一个判断才可以完成线程的停止。关于此知识点在后面有专门的章节进行介绍。

在 Java 中有以下 3 种方法可以终止正在运行的线程：

1.  使用退出标识，使线程正常退出，也就是当 run() 方法完成后线程终止。
2.  使用 stop() 方法强行终止线程，但是不推荐使用这个方法，因为 stop() 和 suspend() 及 resume() 一样，都是作废过期的方法，使用它们可能产生不可预料的结果。
3.  使用 interrupt() 方法中断线程。

## 停止不了的线程

interrupt() 方法的作用是用来停止线程，但 intermpt() 方法的使用效果并不像循环结构中 break 语句那样，可以马上停止循环。调用 intermpt() 方法仅仅是在当前线程中打了一个停止的标记，并不是真的停止线程。

#### 例 1

下面通过一个案例演示 interrupt() 方法停止线程的用法。案例用到的线程非常简单，仅仅是实现输出从 1~10000 的整数，代码如下：

```
package ch14;
public class MyThread13 extends Thread
{
    @Override 
    public void run()
    { 
        super.run(); 
        for (int i=0;i<10000;i++)
        { 
            System.out.println("i="+(i+1)); 
        } 
    } 
}
```

在调用 intermpt() 方法停止 MyThread13 线程之前，首先进行了一个 100 毫秒的休眠。主线程的代码如下：

```
package ch14;
public class Test17
{
    public static void main(String[] args)
    { 
        try
        { 
            MyThread13 thread=new MyThread13();      //创建 MyThread13 线程类实例
            thread.start();    //启动线程
            Thread.sleep(100);    //延时 100 毫秒
            thread.interrupt();    //停止线程
        }
        catch(InterruptedException e)
        { 
            System.out.println("main catch"); 
            e.printStackTrace(); 
        } 
    }
}
```

主线程的运行结果如下所示。从中可以看到，虽然在延时 100 毫秒后调用 intermpt() 方法停止了 thread 线程，但是该线程仍然执行完成输出 10000 行信息。

```
i=1
i=2
...
i=9999
i=10000
```

## 判断线程是不是停止状态

在介绍如何停止线程的知识点前，先来看一下如何判断线程的状态是不是停止的。在 Java 的 SDK 中，Thread.java 类里提供了两种方法。

1.  `this.interrupted()`：测试当前线程是否已经中断。
2.  `this.islnterrupted()`：测试线程是否已经中断。

那么这两个方法有什么区别呢？先来看看 this.intermpted() 方法的解释：测试当前线程是否已经中断，当前线程是指运行 this.interrupted() 方法的线程。为了对此方法有更深入的了解，下面通过一个案例进行说明。

#### 例 2

假设 MyThread14 线程类的代码如下：

```
package ch14;
public class MyThread14 extends Thread
{
    @Override 
    public void run()
    { 
        super.run(); 
        for(int i=0;i<10000;i++)
        { 
            System.out.println("i="+(i+1)); 
        } 
    } 
}
```

主线程的代码如下：

```
package ch14;
public class Test18
{
    public static void main(String[] args)
    {
        try
        {
            MyThread14 thread=new MyThread14();
            thread.start();    //启动线程
            Thread.sleep(100);    //延时 100 毫秒
            thread.interrupt();    //停止线程
            //Thread.currentThread().interrupt();
            System.out.println("是否停止 1？="+thread.interrupted());
            System.out.println("是否停止 2？="+thread.interrupted());
        }
        catch(InterruptedException e)
        {
            System.out.println("main catch");
            e.printStackTrace();
        }
        System.out.println("end!");
    }
}
```

程序运行后的结果如下所示。

```
i=1
i=2
...
i=9999
i=10000
是否停止 1？=false
是否停止 2？=false
end!
```

在主线程中是在 thread 对象上调用以下代码来停止 thread 对象所代表的线程。

```
thread.interrupt();
```

后面又使用以下代码来判断 thread 对象所代表的线程是否停止。

```
System.out.println("是否停止 1 ? ="+thread.interrupted());
System.out.println("是否停止 2 ? ="+thread.interrupted());
```

从控制台打印的结果来看，线程并未停止，这也就证明了 interrupted() 方法的解释：测试当前线程是否已经中断。这个“当前线程”是 main，它从未中断过，所以打印的结果是两个 false。

那么如何使 main 线程产生中断效果呢？再来看一下如下的代码：

```
public static void main(String[] args)
{
    Thread.currentThread().interrupt();
    System.out.println(" 是否停止 1 ? ="+Thread.interrupted());
    System.out.println(" 是否停止 2 ? ="+Thread.interrupted());
    System.out.println("end!");
}
```

程序运行后的结果如下所示。

```
是否停止 1 ? =true
是否停止 2 ? =false end!
```

从上述的结果来看，intermpted() 方法的确用来判断出当前线程是不是停止状态。但为什么第二个布尔值是 false 呢？查看一下官方帮助文档中对 interrupted() 方法的解释如下（斜体显示）：
测试当前线程是否已经中断。线程的中断状态由该方法清除。换句话说，如果连续两次调用该方法，则第二次调用将返回 false（在第一次调用已清除了其中断状态之后，且第二次调用检验完中断状态前，当前线程再次中断的情况除外）。

文档已经解释得很详细，intermpted() 方法具有清除状态的功能，所以第二次调用 interrupted() 方法返回的值是 false。

介绍完 interrupted() 方法后再来看一下 isInterrupted() 方法。isInterrupted() 方法的声明如下：

```
public boolean isInterrupted()
```

从声明中可以看出 isIntermpted() 方法不是 static 的。仍然以 MyThread14 线程为例，这里使用 isInterrupted() 方法来判断线程是否停止，具体代码如下：

```
package ch14;
public class Test18
{
    public static void main(String[] args)
    {
        try
        {
            MyThread14 thread=new MyThread14();
            thread.start();
            Thread.sleep(100);
            thread.interrupt();
            System.out.println("是否停止 1？="+thread.isInterrupted());
            System.out.println("是否停止 2？="+thread.isInterrupted());
        }
        catch(InterruptedException e)
        {
            System.out.println("main catch");
            e.printStackTrace();
        }
        System.out.println("end!");
    }
}
```

程序运行结果如下所示。

```
i=498
是否停止 1？=true
i=499
是否俜止 2？=true
i=500
end!
i=501
i=502
```

从程序的运行结果中可以看到，isInterrupted() 方法并未清除状态标识，所以打印了两个 true。经过上面示例的验证总结一下这两个方法。

1.  this.interrupted()：测试当前线程是否已经是中断状态，执行后具有将状态标识清除为 false 的功能。
2.  this.islnterrupted()：测试线程 Thread 对象是否已经是中断状态，但不清除状态标识。

## 异常法停止线程

有了前面学习过的知识，就可在线程中用 for 语句来判断线程是否为停止状态，如果是停止状态，则后面的代码不再运行。

#### 例 3

下面的线程类 MyThread15 演示了在线程中使用 for 循环，并在循环中调用 intermpted() 方法判断线程是否停止。

```
package ch14;
public class MyThread15 extends Thread
{
    @Override
    public void run()
    {
        super.run();
        for(int i=0;i<500000;i++)
        {
            if(this.interrupted())
            {    //如果当前线程处于停止状态
                System.out.println("已经是停止状态了!我要退出了!");
                break;
            }
            System.out.println("i="+(i+1));
        }
    }
}
```

接下来编写启动 MyThread15 线程的代码，主线程代码如下：

```
package ch14;
public class Test19
{
    public static void main(String[] args)
    {
        try
        { 
            MyThread15 thread=new MyThread15(); 
            thread.start();    //启动线程
            Thread.sleep(2000);    //延时 2000 毫秒
            thread.interrupt();    //停止线程
        }
        catch(InterruptedException e)
        {    //捕捉线程停止异常
            System.out.println("main catch"); 
            e.printStackTrace(); 
        } 
        System.out.println("end!");    //主线程结束时输出
    }
}
```

上述代码启动 MyThread15 线程后延时 2000 毫秒，之后将线程停止。为避免主线程崩溃使用 catch 捕捉了 InterruptedException 异常，此时会输出“main catch”。在主线程执行结束后会输出“end!”。程序执行的输出结果如下所示。

```
......
i=271597
i=271598
已经是停止状态了!我要退出了!
end!
```

从程序执行的结果可以看到，在示例中虽然停止了线程，但如果 for 语句下面还有语句，还是会继续运行的。

下面对 MyThread15 线程进行修改，如下所示是 run() 方法的代码：

```
public void run()
{ 
    super.run(); 
    for(int i=0;i<500000;i++)
    { 
        if(this.interrupted())
        { 
            System.out.println("已经是停止状态了!我要退出了!"); 
            break; 
        } 
        System.out.println("i="+(i+1)); 
    } 
    System.out.println("我被输出，如果此代码是 for 又继续运行，线程并未停止！"); 
}
```

此时的运行效果如下所示，说明线程仍然在继续运行。

```
......
i=233702
i=233703
end!
已经是停止状态了!我要退出了!
我被输出，如果此代码是 for 又继续运行，线程并未停止！
```

那该如何解决线程停止后，语句仍然继续运行的问题呢？解决的办法是在线程中捕捉线程停止异常，如下为修改后的 run() 方法代码。

```
public void run()
{ 
    super.run(); 
    try
    { 
        for(int i=0;i<500000;i++)
        { 
            if(this.interrupted())
            { 
                System.out.println("已经是停止状态了!我要退出了!"); 
                throw new InterruptedException(); 
            } 
            System.out.println("i=" + (i + 1)); 
        } 
        System.out.println("我在 for 下面"); 
    }
    catch(InterruptedException e)
    { 
        System.out.println("进 MyThread15.java 类 run 方法中的 catch 了！"); 
        e.printStackTrace(); 
    } 
}
```

再次运行程序，当线程处于停止状态后，如果 for 循环中的代码继续执行将会拋出 InterruptedException 异常，运行结果如下所示。

```
......
i=251711
i=251712
i=251713
已经是停止状态了!我要退出了!
end!
进 MyThread15.java 类 run 方法中的 catch 了！
java.lang.InterruptedException
    at text.MyThread.run(MyThread.java:16)
```

## 在休眠中停止

如果线程在 sleep() 状态下停止，会是什么效果呢？

#### 例 4

下面通过一个案例来演示这种情况。如下所示为案例中使用的 MyThread16 线程类代码。

```
package ch14;
public class MyThread16 extends Thread
{
    @Override
    public void run()
    {
        super.run();
        try
        {
            System.out.println("run begin");
            Thread.sleep(200000);
            System.out.println("run end");
        }
        catch(InterruptedException e)
        {
            System.out.println("在休眠中被停止!进入 catch!"+this.isInterrupted());
            e.printStackTrace();
        }
    }
}
```

调用 MyThread16 线程的主线程代码如下：

```
package ch14;
public class Test20
{
    public static void main(String[] args)
    {
        try
        {
            MyThread16 thread=new MyThread16();
            thread.start();
            Thread.sleep(200);
            thread.interrupt();
        }
        catch(InterruptedException e)
        {
            System.out.println("main catch");
            e.printStackTrace();
        }
        System.out.println("end!");
    }
}
```

在上述代码中启动 MyThread16 线程后休眠了 200 毫秒，之后调用 interrupt() 方法停止线程，运行结果如下所示。

```
run begin
end!
在休眠中被停止!进入 catch!false
java.lang.InterruptedException: sleep interrupted
    at java.lang.Thread.sleep(Native Method)
    at text.MyThread.run(MyThread.java:12)
```

从运行结果来看，如果在休眠状态下停止某一线程则会拋出进入 InterruptedException 异常，所以会进入 catch 语句块清除停止状态值，使之变成 false。

#### 例 5

这个示例是先休眠再停止线程，下面再编写一个案例来演示先停止再休眠线程的情况。案例使用的 MyThread17 线程类代码如下：

```
package ch14;
public class MyThread17 extends Thread
{
    @Override
    public void run()
    {
        super.run();
        try
        {
            for(int i=0;i<1000;i++)
            {
                System.out.println("i="+(i+1));
            }
            System.out.println("run begin");
            Thread.sleep(200);
            System.out.println("run end");
        }
        catch(InterruptedException e)
        {
            System.out.println("先停止，再遇到了 sleep!进入 catch!");
            e.printStackTrace();
        }
    }
}
```

使用 MyThread17 线程的主线程代码如下：

```
package ch14;
public class Test21
{
    public static void main(String[] args)
    {
        MyThread17 thread=new MyThread17();
        thread.start();
        thread.interrupt();
        System.out.println("end!");
    }
}
```

在上述代码中启动 MyThread17 线程后没有进行延时，马上调用 interrupt() 方法进行停止线程，但是在 MyThread17 线程中有一个 200 毫秒的延时。运行程序后，首先会看到下所示的输出，说明主线程执行完毕。

```
end!
i=1
i=2
i=3
i=4
i=5
i=6
......
```

稍等片刻后，将会看到如下所示的异常，说明线程停止了。

```
......
i=999
i=1000
run begin
先停止，再遇到了 sleep!进入 catch!
java.lang.InterruptedException: sleep interrupted
    at java.lang.Thread.sleep(Native Method)
    at text.MyThread.run(MyThread.java:16)
```

## 强制停止线程

调用 stop() 方法可以在任意情况下强制停止一个线程。下面通过一个案例来演示 stop() 停止线程的方法。

```
package ch14;
public class MyThread18 extends Thread
{
    private int i=0;
    @Override
    public void run()
    {
        try
        {
            while (true)
            {
                i++;
                System.out.println("i=" + i);
                Thread.sleep(1000);
            }
        }
        catch(InterruptedException e)
        {
            //TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
```

如上述代码所示，MyThread18 线程中包含一个死循环，该循环每隔 1000 毫秒执行一次，每次将 i 的值递增 1 并输出。

调用 MyThread18 线程的主线程代码如下：

```
package ch14;
public class Test22
{
    @SuppressWarnings("deprecation")
    public static void main(String[] args)
    {
        try
        {
            MyThread18 thread=new MyThread18();
            thread.start();
            Thread.sleep(8000);
            thread.stop();
        }
        catch(InterruptedException e)
        {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
```

如上述代码所示，MyThread18 线程在启动后有一个 8000 毫秒的延时，在这段时间内会循环 9 次，之后 stop() 方法被执行从而线程停止。运行后的输出如下所示。

```
i=1
i=2
i=3
i=4
i=5
i=6
i=7
i=8
i=9
```

注意：调用 stop() 方法时会抛出 java.lang.ThreadDeath 异常，但在通常情况下，此异常不需要显式地捕捉。

## 释放锁的不良后果

从 JDK 1.6 以后 stop() 方法已经被作废，因为如果强制让线程停止则有可能使一些清理性的工作得不到完成。另外一个情况就是对锁定的对象进行了“解锁”，导致数据得不到同步的处理，出现数据不一致的问题。

使用 stop() 释放锁将会给数据造成不一致性的结果。如果出现这样的情况，程序处理的数据就有可能遭到破坏，最终导致程序执行的流程错误，一定要特别注意。

#### 例 6

下面通过一个案例来演示这种情况。案例使用了一个名为 SynchronizedObject 的实体类，该类代码如下：

```
package ch14;
public class SynchronizedObject
{
    private String username="root";
    private String password="root";
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
    synchronized public void printString(String username,String password)
    {
        try
        {
            this.username=username;
            Thread.sleep(100000);
            this.password=password;
        }
        catch(InterruptedException e)
        {
            e.printStackTrace();
        }
    }
}
```

如上述代码所示，SynchronizedObject 类包含用户名和密码两个成员，printString() 方法用于对这两个成员进行赋值，但是在设置密码之前有一个休眠时间。

下面编写一个线程来对 SynchronizedObject 类进行实例化，并调用 printString() 方法。线程代码如下：

```
package ch14;
public class MyThread19 extends Thread
{
    private SynchronizedObject object;
    public MyThread19(SynchronizedObject object)
    {
        super();
        this.object=object;
    }
    @Override
    public void run()
    {
        object.printString("admin","123456");
    }
}
```

接下来编写主线程代码如下：

```
package ch14;
public class Test23
{
    public static void main(String[] args)
    { 
        try
        { 
            SynchronizedObject object=new SynchronizedObject(); 
            MyThread19 thread=new MyThread19(object); 
            thread.start(); 
            Thread.sleep(500); 
            thread.stop(); 
            System.out.println("用户名："+object.getUsername());
            System.out.println("密码："+object.getPassword()); 
        }
        catch(InterruptedException e)
        { 
            e.printStackTrace();
        } 
    } 
}
```

在上述代码中创建一个 SynchronizedObject 类实例，并将该实例作为参数传递给 MyThread19 线程。MyThread19 线程启动后将立即调用 object.printString('fadminn,"123456") 方法，而在 printString() 方法内有一个较长时间的休眠。该休眠时间大于主线程的休眠时间，所以主线程会继续往下执行，当执行到 stop() 方法时线程被强制停止。

程序最后的运行结果如下所示。

```
用户名：admin
密码：root
```

由于 stop() 方法已经在中被标明是“作废/过期”的方法，显然它在功能上具有缺陷，所以不建议在程序中使用 stop() 方法。

## 使用 return 停止线程

除了上面介绍的方法外，还可以将 intermpt() 方法与 return 结合使用来实现停止线程的效果。

#### 例 7

下面通过一个案例来演示这种情况。如下所示为案例中使用 MyThread20 线程类的代码。

```
package ch14;
public class MyThread20 extends Thread
{
    @Override
    public void run()
    {
        while (true)
        {
            if (this.isInterrupted())
            {
                System.out.println("停止了!");
                return;
            }
            System.out.println("timer="+System.currentTimeMillis());
        }
    }
}
```

调用 MyThread20 线程的主线程代码如下：

```
package ch14;
public class Test24
{
    public static void main(String[] args) throws InterruptedException
    {
        MyThread20 t=new MyThread20();
        t.start();
        Thread.sleep(2000);
        t.interrupt();
    }
}
```

程序执行后的结果如下所示。

```
......
timer=1540977194784
timer=1540977194784
timer=1540977194784
timer=1540977194784
timer=1540977194784
停止了!
```

从程序的执行结果中可以看到成功停止了线程，不过还是建议使用“拋异常”的方法来实现线程的停止，因为在 catch 块中还可以将异常向上拋，使线程停止的事件得以传播。