# Java 线程的优先级和执行顺序

在学习运算符时，读者知道各个运算符之间有优先级，了解运算符的优先级对程序幵发有很好的作用。线程也是如此，每个线程都具有优先级，Java 虚拟机根据线程的优先级决定线程的执行顺序，这样使多线程合理共享 CPU 资源而不会产生冲突。

## 优先级概述

在 Java 语言中，线程的优先级范围是 1~10，值必须在 1~10，否则会出现异常；优先级的默认值为 5。优先级较高的线程会被优先执行，当执行完毕，才会轮到优先级较低的线程执行。如果优先级相同，那么就采用轮流执行的方式。

可以使用 Thread 类中的 setPriority() 方法来设置线程的优先级。语法如下：

```
public final void setPriority(int newPriority);
```

如果要获取当前线程的优先级，可以直接调用 getPriority() 方法。语法如下：

```
public final int getPriority();
```

## 使用优先级

简单了解过优先级之后，下面通过一个简单的例子来演示如何使用优先级。

#### 例 1

分别使用 Thread 类和 Runnable 接口创建线程，并为它们指定优先级。

(1) 创建继承自 Thread 类的 FirstThreadInput 类，重写该类的 run() 方法。代码如下：

```
package ch14;
public class FirstThreadInput extends Thread
{
    public void run()
    {
        System.out.println("调用 FirstThreadInput 类的 run()重写方法");    //输出字符串
        for(int i=0;i<5;i++)
        {
            System.out.println("FirstThreadInput 线程中 i="+i);    //输出信息
            try
            {
                Thread.sleep((int) Math.random()*100);    //线程休眠
            }
            catch(Exception e){}
        }
    }
}
```

(2) 创建实现 Runnable 接口的 SecondThreadInput 类，实现 run() 方法。代码如下：

```
package ch14;
public class SecondThreadInput implements Runnable
{
    public void run()
    {
        System.out.println("调用 SecondThreadInput 类的 run()重写方法");    //输出字符串
        for(int i=0;i<5;i++)
        {
            System.out.println("SecondThreadInput 线程中 i="+i);    //输出信息
            try
            {
                Thread.sleep((int) Math.random()*100);    //线程休眠
            }
            catch(Exception e){}
        }
    }
}
```

(3) 创建 TestThreadInput 测试类，分别使用 Thread 类的子类和 Runnable 接口的对象创建线程，然后调用 setPriority() 方法将这两个线程的优先级设置为 4，最后启动线程。代码如下：

```
package ch14;
public class TestThreadInput
{
    public static void main(String[] args)
    {
        FirstThreadInput fti=new FirstThreadInput();
        Thread sti=new Thread(new SecondThreadInput());
        fti.setPriority(4);
        sti.setPriority(4);
        fti.start();
        sti.start();
    }
}
```

(4) 运行上述代码，运行结果如下所示。

```
调用 FirstThreadInput 类的 run()重写方法
调用 SecondThreadInput 类的 run()重写方法
FirstThreadInput 线程中 i=0
SecondThreadInput 线程中 i=0
FirstThreadInput 线程中 i=1
FirstThreadInput 线程中 i=2
SecondThreadInput 线程中 i=1
FirstThreadInput 线程中 i=3
SecondThreadInput 线程中 i=2
FirstThreadInput 线程中 i=4
SecondThreadInput 线程中 i=3
SecondThreadInput 线程中 i=4
```

由于该例子将两个线程的优先级都设置为 4，因此它们交互占用 CPU ，宏观上处于并行运行状态。

重新更改 ThreadInput 类的代码、设置优先级。代码如下：

```
fti.setPriority(1);
sti.setPriority(10);
```

重新运行上述代码，如下所示。

```
调用 FirstThreadInput 类的 run()重写方法
调用 SecondThreadInput 类的 run()重写方法
FirstThreadInput 线程中 i=0
SecondThreadInput 线程中 i=0
SecondThreadInput 线程中 i=1
SecondThreadInput 线程中 i=2
SecondThreadInput 线程中 i=3
SecondThreadInput 线程中 i=4
FirstThreadInput 线程中 i=1
FirstThreadInput 线程中 i=2
FirstThreadInput 线程中 i=3
FirstThreadInput 线程中 i=4
```