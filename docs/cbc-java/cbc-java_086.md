# Java 数组填充（fill()）

Arrays 类提供了一个 fill() 方法，可以在指定位置进行数值填充。fill() 方法虽然可以填充数组，但是它的功能有限制，只能使用同一个数值进行填充。语法如下：

```
Arrays.fill(array,value);
```

其中，array 表示数组，value 表示填充的值。

#### 例 1

声明一个 int 类型的 number 数组，然后通过 for 语句进行遍历，在该语句中调用 Arrays 类的 fill() 方法来填充数组，并输出数组中元素的值。代码如下：

```
public static void main(String[] args)
{
    int[] number=new int[5];
    System.out.println("number —共有 "+number.length+" 个元素，它们分别是：");
    for(int i=0;i<number.length;i++)
    {
        Arrays.fill(number,i);
        System.out.println("number["+i+"]="+i);
    }
}
```

执行上述代码，输出结果如下所示。

```
number 一共有 5 个元素，它们分别是：
number[0]=0
number[1]=1
number[2]=2
number[3]=3
number[4]=4
```

注意：在向数组中填充数组元素时要注意，虽然定义的数组长度为 6，但是数组的下标是从 0 开始的，因此数组的最大下标值应该是 5，此时如果为下标为 6 的数组元素赋值，则会出现数组下标越界异常。