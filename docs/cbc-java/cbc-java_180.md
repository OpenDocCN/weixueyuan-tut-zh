# Java yieId()方法如何使用

yieId() 方法的作用是放弃当前的 CPU 资源，将它让给其他的任务去占用 CPU 执行时间。但放弃的时间不确定，有可能刚刚放弃，马上又获得 CPU 时间片。

#### 例 1

创建一个线程实现从 1 开始，共累加 5 千万次，要求在线程中输出开始运算时间、结束运算时间以及运算耗时时间。具体实现代码如下：

```
package ch14;
public class MyThread12 extends Thread
{
    @Override
    public void run()
    {
        long beginTime=System.currentTimeMillis();
        System.out.println("线程开始执行时间："+beginTime);
        int count=0;
        for (int i=0;i<50000000;i++)
        {
            //Thread.yield();    //调用 yield()方法，暂时不用
            count=count+(i+1);
        }
        long endTime=System.currentTimeMillis();
        System.out.println("线程结束执行时间："+endTime);
        System.out.println("本次执行用时："+(endTime-beginTime)+"毫秒！");
    }
}
```

编写主线程代码启动上面的 MyThread12 线程，代码如下：

```
package ch14;
public class Test16
{
    public static void main(String[] args)
    {
        MyThread12 thread=new MyThread12();    //创建 MyThread12 线程实例
        thread.start();    //启动线程
    }
}
```

在 MyThread12 线程类中调用 yield() 方法的代码被注释了，此时运行结果如下所示。

```
线程开始执行时间：1540965620705
线程结束执行时间：1540965620729
本次执行用时：24 毫秒！
```

下面取消对调用 yieId() 方法的代码注释，再次运行程序，此时运行结果如下所示。

```
线程开始执行时间：1540965685288
线程结束执行时间：1540965696100
本次执行用时：10812 毫秒！
```

对比两次运行结果可以看到，调用 yieId() 方法后，线程的执行时间明显变长。