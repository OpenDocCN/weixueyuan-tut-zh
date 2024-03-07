# Java 数组排序：Java 快速排序（Quicksort）法

快速排序（Quicksort）是对冒泡排序的一种改进。

快速排序的基本思想是：通过一趟排序，将要排序的数据分隔成独立的两部分，其中一部分的所有数据比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此使整个数据变成有序序列。

具体做法是：假设要对某个数组进行排序，首先需要任意选取一个数据（通常选用第一个数据）作为关键数据，然后将所有比它小的数都放到它的前面，所有比它大的数都放到它的后面。这个过程称为一趟快速排序；递归调用此过程，即可实现数据的快速排序。

#### 例 1

利用快速排序法对一数组进行排序，实现步骤如下。

(1) 声明静态的 getMiddle() 方法，该方法需要返回一个 int 类型的参数值，在该方法中传入 3 个参数。代码如下：

```
public static int getMiddle(int[] list,int low,int high)
{
    int tmp=list[low];    //数组的第一个值作为中轴（分界点或关键数据）
    while(low<high)
    {
        while(low<high&&list[high]>tmp)
        {
            high--;
        }
        list[low]=list[high];    //比中轴小的记录移到低端
        while(low<high&&list[low]<tmp)
        {
            low++;
        }
        list[high]=list[low];    //比中轴大的记录移到高端
    }
    list[low]=tmp;    //中轴记录到尾
    return low;    //返回中轴的位置
}
```

(2) 创建静态的 unckSort() 方法，在该方法中判断 low 参数是否小于 high 参数，如果是则调用 getMiddle() 方法，将数组一分为二，并且调用自身的方法进行递归排序。代码如下：

```
public static void unckSort(int[] list,int low,int high)
{
    if(low<high)
    {
        int middle=getMiddle(list,low,high);    //将 list 数组一分为二
        unckSort(list,low,middle-1);    //对低字表进行递归排序
        unckSort(list,middle+1,high);    //对高字表进行递归排序
    }
}
```

(3) 声明静态的 quick() 方法，在该方法中判断传入的数组是否为空，如果不为空，则调用 unckSort() 方法进行排序。代码如下：

```
public static void quick(int[] str)
{
    if(str.length>0)
    {
        //查看数组是否为空
        unckSort(str,0,str.length-1);
    }
}
```

(4) 在 main() 方法中声明 int 类型的 number 数组，接着输出该数组中的元素。然后调用自定义的 quick() 方法进行排序，排序后重新输出数组中的元素。代码如下：

```
int[] number={13,15,24,99,14,11,1,2,3};
System.out.println("排序前：");
for(int val:number)
{
    System.out.print(val+" ");
}
quick(number);
System.out.println("\n 排序后：");
for(int val:number)
{
    System.out.print(val +" ");
}
```

运行前面的代码进行测试，输出结果如下：

```
排序前：
13 15 24 99 14 11 1 2 3
排序后：
1 2 3 11 13 14 15 24 99 
```