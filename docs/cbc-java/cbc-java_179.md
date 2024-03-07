# Java getId()方法的作用

getId() 方法的作用非常简单，就是取得正在运行线程的唯一标识。如下代码演示了 getId() 方法的使用：

```
package ch14;
public class Test15
{
    public static void main(String[] args)
    {
        Thread runThread=Thread.currentThread();
        System.out.println("当前线程名称："+runThread.getName());
        System.out.println("当前线程标识："+runThread.getId());
    }
}
```

从如下所示的输出结果来看，当前执行代码的线程名称为 main，线程 id 值为 1。

```
当前线程名称：main
当前线程标识：1
```