# Java 数组排序：Java 直接插入法

直接插入排序的基本思想是：将 n 个有序数存放在数组 a 中，要插入的数为 x，首先确定 x 插在数组中的位置 p，然后将 p 之后的元素都向后移一个位置，空出 a(p)，将 x 放入 a(p)，样可实现插入 x 后仍然有序。

#### 例 1

本例子通过直接插入的方法对上述例子中的 number 数组进行排序。创建一个 Test27 类文件，在 main() 方法中开始编码，具体实现代码如下：

```
public static void main(String[] args)
{
    int[] number={13,15,24,99,4,1};
    System.out.println("排序前：");
    for(int val:number)
    {    //遍历数组元素
        System.out.print(val+" ");    //输出数组元素
    }
    int temp,j;
    for(int i=1;i<number.length;i++)
    {
        temp=number[i];
        for(j=i-1;j>=0&&number[j]>temp;j--)
        {
            number[j+1]=number[j];
        }
        number[j+1]=temp;
    }
    System.out.println("\n 排序后：");
    for(int val:number)
    {    //遍历数组元素
        System.out.print(val+" ");    //输出数组元素
    }
}
```

在上述代码中，首先在控制台输出 number 数组中的元素，然后通过 for 循环对数组中的元素进行排序，最后再次输出排序后的元素。

执行上述代码，最终的输出结果如下：

```
排序前：
13 15 24 99 4 1
排序后：
1 4 13 15 24 99 
```