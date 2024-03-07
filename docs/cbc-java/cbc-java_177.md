# Java isAlive()方法的作用

isAlive() 方法的作用是判断当前的线程是否处于活动状态。什么是活动状态呢？活动状态就是线程已经启动且尚未终止。线程处于正在运行或准备开始运行的状态，就认为线程是“存活”的。

#### 例 1

下面通过一个案例来理解 isAlive() 方法判断线程是否活动的标准。假设 MyThread08 线程类的代码如下：

```
package ch14;
public class MyThread08 extends Thread
{
    @Override 
    public void run()
    { 
        System.out.println("run="+this.isAlive()); 
    }
}
```

如上述代码所示，MyThread08 类的代码非常简单，只是在 run() 方法中调用 isAlive() 方法输出当前线程的活动状态。

下面编写启动 MyThread08 线程的代码，具体实现如下：

```
package ch14;
public class Test11
{
    public static void main(String[] args)
    {
        MyThread08 mythread=new MyThread08();    //创建一个 MyThread08 线程实例
        System.out.println("begin=="+mythread.isAlive());    //输出线程状态
        mythread.start();    //启动线程
        System.out.println("end=="+mythread.isAlive());    //输出线程状态
    }
}
```

执行后的输出结果如下：

```
begin==false
end==true
run=true
```

在这里需要注意如下一行代码：

```
System.out.println("end=="+mythread.isAlive());
```

虽然在本示例中这行语句输出的值是 true，但此值是不确定的。输出 true 值是因为 mythread 线程还未执行完毕，所以输出 true。如果代码更改成如下形式：

```
package ch14;
public class Test11
{
    public static void main(String[] args) throws InterruptedException
    { 
        MyThread08 mythread=new MyThread08();    //创建一个 MyThread08 线程实例
        System.out.println("begin=="+mythread.isAlive());    //输出线程状态
        mythread.start();    //启动线程
        Thread.sleep(1000);     //延时 1000 毫秒
        System.out.println("end=="+mythread.isAlive());    //输出线程状态
    }
}
```

再次运行主线程，此时的输出结果如下：

```
begin==false
run=true
end==false
```

此时 end 的返回值为 false，这是因为 mythread 对象已经在 1 秒之内执行完毕。

#### 例 2

在使用 isAlive() 方法时，如果将线程对象以构造参数的方式传递给 Thread 对象进行 start() 启动，运行的结果和前面示例是有差异的。造成这样的差异的原因还是来自于 Thread.currentThread() 和 this 的差异。下面通过一个案例来验证这一点。

(1) 首先创建案例所使用的线程类，这里命名为 MyThread09，具体代码如下：

```
package ch14;
public class MyThread09 extends Thread
{
    public MyThread09()
    {
        System.out.println("构造方法---开始");
        System.out.println("Thread.currentThread().getName()="+Thread.currentThread().getName());
        System.out.println("Thread.currentThread().isAlive()="+Thread.currentThread().isAlive());
        System.out.println("this.getName()="+this.getName());
        System.out.println("this.isAlive()="+this.isAlive());
        System.out.println("构造方法---结束");
    }
    @Override
    public void run()
    {
        System.out.println("run()方法---开始");
        System.out.println("Thread.currentThread().getName()="+Thread.currentThread().getName());
        System.out.println("Thread.currentThread().isAlive()="+Thread.currentThread().isAlive());
        System.out.println("this.getName()="+this.getName());
        System.out.println("this.isAlive()="+this.isAlive());
        System.out.println("run()方法---结束");
    }
}
```

(2) 接下来编写主线程代码，创建 MyThread09 线程实例并启动线程。代码如下：

```
package ch14;
public class Test12
{
    public static void main(String[] args)
    {
        MyThread09 mythread=new MyThread09();    //创建一个 MyThread09 线程实例
        Thread t1=new Thread(mythread);    //创建一个线程
        System.out.println("main begin t1 isAlive="+t1.isAlive());    //输出线程状态
        t1.setName("A");    //设置线程名称
        t1.start();    //启动线程
        System.out.println("main end t1 isAlive="+t1.isAlive());
    }
}
```

程序运行结果如下：

```
构造方法---开始
Thread.currentThread().getName()=main
Thread.currentThread().isAlive()=true
this.getName()=Thread-0
this.isAlive()=false
构造方法---结束
main begin t1 isAlive=false
main end t1 isAlive=true
run()方法---开始
Thread.currentThread().getName()=A
Thread.currentThread().isAlive()=true
this.getName()=Thread-0
this.isAlive()=false
run()方法---结束
```