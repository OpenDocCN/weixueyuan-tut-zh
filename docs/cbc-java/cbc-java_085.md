# Java 比较两个数组是否相等（equals()）

数组相等的条件不仅要求数组元素的个数必须相等，而且要求对应位置的元素也相等。Arrays 类提供了 equals() 方法比较整个数组。语法如下：

```
Arrays.equals(arrayA, arrayB);
```

其中，arrayA 是用于比较的第一个数组，arrayB 是用于比较的第二个数组。

#### 例 1

下面代码演示 Arrays 类的 equals() 方法的使用。

```
public static void main(String[] args)
{
    double[] score1={99,100,98.5,96.5,72};
    double[] score2=new double[5];
    score2[0]=99;
    score2[1]=100;
    score2[2]=98.5;
    score2[3]=96.5;
    score2[4]=72;
    double[] score3={99,96.5,98.5,100,72};
    if(Arrays.equals(score1,score2))
    {
        System.out.println("score1 数组和 score2 数组相等");
    }
    else
    {
        System.out.println("score1 数组和 score2 数组不等");
    }
    if(Arrays.equals(score1,score3))
    {
        System.out.println("score1 数组和 score3 数组相等");
    }
    else
    {
        System.out.println("score1 数组和 score3 数组不等");
    }
}
```

上述代码中定义 3 个数组，分别为 score1、score2 和 score3。第一个数组直接给出了数组的值；第二个数组先定义数组的长度，然后为每个元素赋值；第三个数组中的元素和第一个数组中的元素相同，但是顺序不同。分别将 score1 数组与 score2 和 score3 数组进行比较，并输出比较的结果。

运行上述代码，输出结果如下：

```
score1 数组和 score2 数组相等
score1 数组和 score3 数组不等
```