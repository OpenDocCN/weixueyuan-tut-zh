# Java 数组排序：Java 冒泡排序（Bubble Sort）法

冒泡排序（Bubble Sort）是常用的数组排序算法之一，它以简洁的思想与实现方法而备受青睐，也是广大学习者最先接触的一种排序算法。

冒泡排序的基本思想是：对比相邻的元素值，如果满足条件就交换元素值，把较小的元素值移动到数组前面，把大的元素值移动到数组后面（也就是交换两个元素的位置），这样数组元素就像气泡一样从底部上升到顶部。

冒泡排序的算法比较简单，排序的结果稳定，但时间效率不太高。Java 中的冒泡排序在双层循环中实现，其中外层循环控制排序轮数，总循环次数为要排序数组的长度减 1。而内层循环主要用于对比相邻元素的大小，以确定是否交换位置，对比和交换次数依排序轮数而减少。

#### 例 1

获取用户在控制台输入的 5 个成绩信息，将这些成绩保存到数组中，然后对数组应用冒泡排序，并输出排序后的结果，实现步骤如下。

(1) 创建一个 Test24 类文件，在 main() 方法中开始编码。首先创建 Scanner 类的实例后声明 double 类型的 score 数组，然后接收用户在控制台输入的成绩，并保存到元素中。代码如下：

```
Scanner scan=new Scanner(System.in);
double[] score=new double[5];
for(int i=0;i<score.length;i++)
{
    System.out.print("请输入第 "+(i+1)+" 个成绩：");
    score[i]=scan.nextDouble();
}
```

(2) 在对 score 数组排序之前，首先输出数组中各个元素的值。代码如下：

```
System.out.println("排序前的元素值：");
for(double val:score)
{
    System.out.print(val+"\t");
}
System.out.println();
```

(3) 通过冒泡排序方法实现对 score 数组的排序，在实现时需要借助一个临时变量。代码如下：

```
System.out.println("通过冒泡排序方法对数组进行排序：");
for(int i=0;i<score.length-1;i++)
{
    //比较相邻两个元素，较大的数往后冒泡
    for (int j=0;j<score.length-1-i;j++)
    {
        if(score[j]>score[j+1])
        {
            double temp=score[j+1];    //把第一个元素值保存到临时变量中
            score[j+1]=score[j];    //把第二个元素值转移到第一个元素变量中
            score[j]=temp;    //把临时变量（第一个元素的原值）保存到第二个元素中
        }
        System.out.print(score[j]+ " ");    //对排序后的数组元素进行输出
    }
    System.out.print("【");
    for(int j=score.length-1-i;j<score.length;j++)
    {
        System.out.print(score[j]+" ");
    }
    System.out.println("】");
}
```

(4) 运行前面的代码进行测试，如下所示。

```
请输入第 1 个成绩：77
请输入第 2 个成绩：90
请输入第 3 个成绩：68
请输入第 4 个成绩：59
请输入第 5 个成绩：80
排序前的元素值：
77.0    90.0    68.0    59.0    80.0   
通过冒泡排序方法对数组进行排序：
77.0 68.0 59.0 80.0 【90.0 】
68.0 59.0 77.0 【80.0 90.0 】
59.0 68.0 【77.0 80.0 90.0 】
59.0 【68.0 77.0 80.0 90.0 】
```