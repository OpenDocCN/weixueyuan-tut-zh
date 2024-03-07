# Java 暂停/挂起线程（suspend()）和恢复线程（resume()）

暂停线程意味着此线程还可以恢复运行。在 Java 多线程中，可以使用 suspend() 方法暂停线程，使用 resume() 方法恢复线程的执行。

## suspend() 与 resume() 方法

本节通过一个案例来介绍 suspend() 与 resume() 方法的用法。首先来看一下案例中使用到的 MyThread21 线程，代码如下所示。

```
package ch14;
public class MyThread21 extends Thread
{
    private long i=0;
    public long getI()
    {
        return i;
    }
    public void setI(long i)
    {
        this.i=i;
    }
    @Override
    public void run()
    {
        while(true)
        {
            i++;
        }
    }
}
```

MyThread21 线程中有一个成员 i，其中 setI() 方法和 getI() 方法分别用于设置和获取 i 的值，run() 方法则是一个从 i 开始递增的死循环。

下面编写主线程的代码，具体如下所示。

```
package ch14;
public class Test25
{
    public static void main(String[] args)
    {
        try
        {
            MyThread21 thread=new MyThread21();
            thread.start();
            Thread.sleep(5000);
            //A 段
            thread.suspend();
            System.out.println("A= "+System.currentTimeMillis()+" i= "+thread.getI());
            Thread.sleep(5000);
            System.out.println("A= "+System.currentTimeMillis()+" i= "+thread.getI());
            //B 段
            thread.resume();
            Thread.sleep(5000);
            //C 段
            thread.suspend();
            System.out.println("B= "+System.currentTimeMillis()+" i= "+thread.getI());
            Thread.sleep(5000);
            System.out.println("B= "+System.currentTimeMillis()+" i= "+thread.getI());
        }
        catch(InterruptedException e)
        {
            e.printStackTrace();
        }
    }
}
```

最终运行结果如下所示。

```
A= 1540978346179 i= 2680986095
A= 1540978351179 i= 2680986095
B= 1540978356179 i= 5348657508
B= 1540978361179 i= 5348657508
```

从输出结果的时间来看，调用 suspend() 方法确实可以暂停线程，而在调用 resume() 方法后线程恢复运行状态。

## 独占问题

在使用 suspend() 方法与 resume() 方法时，如果使用不当极易造成公共的同步对象被独占，从而使得其他线程无法访问公共同步对象。

#### 例 2

下面通过一个案例来演示这种情况。如下所示是案例中使用的公共对象的代码。

```
package ch14;
public class SynchronizedObject1
{
    synchronized public void printString()
    {
        System.out.println("begin");
        if (Thread.currentThread().getName().equals("a"))
        {
            System.out.println("a 线程永远 suspend 了！");
            Thread.currentThread().suspend();
        }
        System.out.println("end");
    }
}
```

SynchronizedObject1 类在 printString() 方法开始时输出“begin”，在该方法结束时输出“end”，方法体中的 if 判断如果当前线程的名称是 a 则调用 suspend() 方法暂停线程。

接下来编写主线程代码，在主线程中创建一个 SynchronizedObject1 类对象并在线程中调用 printString() 方法，具体代码如下所示。

```
package ch14;
public class Test26
{
    public static void main(String[] args)
    {
        try
        {
            final SynchronizedObject1 object=new SynchronizedObject1();
            Thread thread1=new Thread()
            {
                @Override
                public void run()
                {
                    object.printString();
                }
            };
            thread1.setName("a");
            thread1.start();
            Thread.sleep(1000);
            Thread thread2=new Thread()
            {
                @Override
                public void run()
                {
                    System.out.println("thread2 启动了，但进入不了 printString()方法！所以只会打印 1 个 begin！");
                    System.out.println("因为 printString()方法被 a 线程锁定并且永远暂停了！");
                    object.printString();
                }
            };
            thread2.start();
        }
        catch(InterruptedException e)
        {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
```

上述代码比较简单这里就不再解释，运行后的结果如下所示。从中可以看到由于线程被永久暂停，所以只会输出一个 begin。

```
begin
a 线程永远 suspend 了！
thread2 启动了，但进入不了 printString()方法！所以只会打印 1 个 begin！
因为 printString()方法被 a 线程锁定并且永远暂停了！
```

#### 例 3

还有另外一种独占锁的情况也要格外注意，稍有不慎就会掉进“坑”里。创建测试用的 MyThread22 线程，具体代码如下：

```
package ch14;
public class MyThread22 extends Thread
{
    private long i=0;
    @Override
    public void run()
    {
        while (true)
        {
            i++;
        }
    }
}
```

再来看主线程的代码，如下所示。

```
package ch14;
public class Test27
{
    public static void main(String[] args)
    {
        try
        {
            MyThread22 thread=new MyThread22();
            thread.start();
            Thread.sleep(1000);
            thread.suspend();
            System.out.println("main end!");
        }
        catch(InterruptedException e)
        {
            e.printStackTrace();
        }
    }
}
```

程序执行后将看到预料不到的结果，如下所示。

```
main end!
```

如果将 MyThread22 线程类的代码更改如下：

```
package ch14;
public class MyThread22 extends Thread
{   
    private long i=0; 
    @Override 
    public void run()
    { 
        while(true)
        { 
            i++; 
            System.out.println(i); 
        } 
    }
}
```

再次运行程序，控制台将不打印 main end，运行结果如下所示。

```
......
130862
130863
130864
130865
130866
130867
```

出现这种情况的原因是，当程序运行到 println() 方法内部停止时，同步锁未被释放。这导致当前 PrintStream 对象的 println() 方法一直呈“暂停”状态，并且“锁未释放”，而 main() 方法中的代码“System.out. println(Mmain end!'1);”迟迟不能执行打印。

提示：虽然 suspend() 方法是过期作废的方法，但还是有必要研究它过期作废的原因，这是很有意义的。

## 不同步问题

在使用 suspend() 方法与 resume() 方法时也容易出现因为线程的暂停而导致数据不同步的情况。

#### 例 4

下面通过一个案例来演示这种情况。如下所示是案例中使用的公共对象的代码。

```
package ch14;
public class MyObject
{
    private String username="1";
    private String password="11";
    public void setValue(String u,String p)
    {
        this.username=u;
        if(Thread.currentThread().getName().equals("a"))
        {
            System.out.println("停止 a 线程！");
            Thread.currentThread().suspend();
        }
        this.password=p;
    }
    public void printUsernamePassword()
    {
        System.out.println(username+" "+password);
    }
}
```

如上述代码所示，MyObject 类的 setValue() 方法会在线程名称是 a 时执行停止线程操作。 如下所示是主线程代码。

```
package ch14;
public class Test28
{
    public static void main(String[] args) throws InterruptedException
    {
        final MyObject myobject=new MyObject();
        Thread thread1=new Thread()
        {
            public void run()
            {
                myobject.setValue("a","aa");
            };
        };
        thread1.setName("a");
        thread1.start();
        Thread.sleep(500);
        Thread thread2=new Thread()
        {
            public void run()
            {
                myobject.printUsernamePassword();
            };
        };
        thread2.start();
    }
}
```

程序运行结果如下所示。

```
停止 a 线程！
a 11
```

从程序运行的结果可以看到，出现了值不同步的情况，所以在程序中使用 suspend() 方法要格外注意。