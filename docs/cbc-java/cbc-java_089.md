# Java 数组排序：Java sort()方法

在 Java 中实现数组排序的方式很多，除了利用 Arrays 类的 sort() 方法外，还可以编写自定义方法来实现自己的排序算法。本文介绍如何使用 Arrays 类提供的 sort() 方法来对数组进行排序。

使用 java.util.Arrays 类中的 sort() 方法对数组进行排序分为以下两步：

1.  导入 java.util.Arrays 包。
2.  使用 Armys.sort(数组名) 语法对数组进行排序，排序规则是从小到大，即升序。

#### 例 1

假设在数组 scores 中存放了 5 名学生的成绩，现在要实现从低到高排列的功能。在这里使用 Arrays.sort() 方法来实现，具体代码如下：

```
import java.util.Arrays;
public class Test23
{
    public static void main(String[] args)
    {
        //定义含有 5 个元素的数组
        double[] scores=new double[]{78,45,85,97,87};
        System.out.println("排序前数组内容如下：");

        //对 scores 数组进行循环遍历
        for(int i=0;i<scores.length;i++)
        {
            System.out.print(scores[i]+"\t");
        }
        System.out.println("\n 排序后的数组内容如下：");

        //对数组进行排序
        Arrays.sort(scores);

        //遍历排序后的数组
        for(int j=0;j<scores.length;j++)
        {
            System.out.print(scores[j]+"\t");
        }
    }
}
```

如上述代码所示，要对一个数组进行升序排列，只需要调用 Arrays.sort() 方法即可。运行后的输出结果如下所示。

```
排序前数组内容如下：
78.0    45.0    85.0    97.0    87.0   
排序后的数组内容如下：
45.0    78.0    85.0    87.0    97.0
```