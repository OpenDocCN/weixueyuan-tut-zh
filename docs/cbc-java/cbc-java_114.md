# Java 析构方法

析构方法与构造方法相反，当对象脱离其作用域时（例如对象所在的方法已调用完毕），系统自动执行析构方法。析构方法往往用来做清理垃圾碎片的工作，例如在建立对象时用 new 开辟了一片内存空间，应退出前在析构方法中将其释放。

在 Java 的 Object 类中还提供了一个 protected 类型的 finalize() 方法，因此任何 Java 类都可以覆盖这个方法，在这个方法中进行释放对象所占有的相关资源的操作。

对象的 finalize() 方法具有如下特点：

*   垃圾回收器是否会执行该方法以及何时执行该方法，都是不确定的。
*   finalize() 方法有可能使用对象复活，使对象恢复到可触及状态。
*   垃圾回收器在执行 finalize() 方法时，如果出现异常，垃圾回收器不会报告异常，程序继续正常运行。

例如：

```
protected void finalize()
{
    //对象的清理工作
}
```

#### 例 1

下面通过一个例子来讲解析构方法的使用。该例子计算从类中实例化对象的个数。

(1) Counter 类在构造方法中增值，在析构方法中减值。如下所示为计数器类 Counter 的代码：

```
public class Counter
{
    private static int count=0;    //计数器变量
    public Counter()
    {
        //构造方法
        this.count++;    //创建实例时增加值
    }
    public int getCount()
    {
        //获取计数器的值
        return this.count;
    }
    protected void finalize()
    {
        //析构方法
        this.count--;    //实例销毁时减少值
        System.out.println("对象销毁");
    }
}
```

(2) 创建一个带 main() 的 TestCounter 类对计数器进行测试，示例代码如下：

```
public class TestCounter
{
    public static void main(String[] args)
    {
        Counter cnt1=new Counter();    //建立第一个实例
        System.out.println("数量："+cnt1.getCount());    //输出 1
        Counter cnt2=new Counter();    //建立第二个实例
        System.out.println("数量："+cnt2.getCount());    //输出 2
        cnt2=null;    //销毁实例 2
        try
        {
            System.gc();    //清理内存
            Thread.currentThread().sleep(1000);    //延时 1000 毫秒
            System.out.println("数量："+cnt1.getCount());    //输出 1
        }
        catch(InterruptedException e)
        {
            e.printStackTrace();
        }
    }
}
```

执行后输出结果如下：

```
数量：1
数量：2
对象销毁
数量：1
```

技巧：由于 finalize() 方法的不确定性，所以在程序中可以调用 System.gc() 或者 Runtime.gc() 方法提示垃圾回收器尽快执行垃圾回收操作。