# Java 复制数组的四种方法：arraycopy()方法、clone() 方法、copyOf()和 copyOfRange()方法

所谓复制数组，是指将一个数组中的元素在另一个数组中进行复制。

在 Java 中实现数组复制有 4 种方法，分别为使用 Arrays 类的 copyOf() 方法和 copyOfRange() 方法、System 类的 arraycopy() 方法和 Object 类的 clone() 方法。下面来详细介绍这 4 种方法的使用。

## 使用 copyOf() 方法和 copyOfRange() 方法

Arrays 类的 copyOf() 方法与 copyOfRange() 方法都可实现对数组的复制。copyOf() 方法是复制数组至指定长度，copyOfRange() 方法则将指定数组的指定长度复制到一个新数组中。

#### 1\. 使用 copyOf() 方法对数组进行复制

Arrays 类的 copyOf() 方法的语法格式如下：

```
Arrays.copyOf(dataType[] srcArray,int length);
```

其中，srcArray 表示要进行复制的数组，length 表示复制后的新数组的长度。

使用这种方法复制数组时，默认从源数组的第一个元素（索引值为 0）开始复制，目标数组的长度将为 length。如果 length 大于 srcArray.length，则目标数组中采用默认值填充；如果 length 小于 srcArray.length，则复制到第 length 个元素（索引值为 length-1）即止。

注意：目标数组如果已经存在，将会被重构。

#### 例 1

假设有一个数组中保存了 5 个成绩，现在需要在一个新数组中保存这 5 个成绩，同时留 3 个空余的元素供后期开发使用。

使用 Arrays 类的 CopyOf() 方法完成数组复制的代码如下：

```
import java.util.Arrays;
public class Testl9
{
    public static void main(String[] args)
    {
        //定义长度为 5 的数组
        int scores[]=new int[]{57,81,68,75,91};

        //输出源数组
        System.out.println("源数组内容如下：");

        //循环遍历源数组
        for(int i=0;i<scores.length;i++)
        {
            //将数组元素输出
            System.out.print(scores[i]+"\t");
        }

        //定义一个新的数组，将 scores 数组中的 5 个元素复制过来
        //同时留 3 个内存空间供以后开发使用
        int[] newScores=(int[])Arrays.copyOf(scores,8);
        System.out.println("\n 复制的新数组内容如下：");

        //循环遍历复制后的新数组
        for(int j=0;j<newScores.length;j++)
        {
            //将新数组的元素输出
            System.out.print(newScores[j]+"\t");
        }
    }
}
```

在上述代码中，由于源数组 scores 的长度为 5，而要复制的新数组 newScores 的长度为 8，因此在将源数组中的 5 个元素复制完之后，会采用默认值填充剩余 3 个元素的内容。

因为源数组 scores 的数据类型为 int，而使用 Arrays.copyOf(scores,8) 方法复制数组之后返回的是 Object[] 类型，因此需要将 Object[] 数据类型强制转换为 int[] 类型。同时，也正因为 scores 的数据类型为 int，因此默认值为 0。

运行的结果如下所示。

```
源数组内容如下：
57    81    68    75    91   
复制的新数组内容如下：
57    81    68    75    91    0    0    0
```

#### 2\. 使用 CopyOfRange() 方法对数组进行复制

Arrays 类的 CopyOfRange() 方法是另一种复制数组的方法，其语法形式如下：

```
Arrays.copyOfRange(dataType[] srcArray,int startIndex,int endIndex)
```

其中，srcArray 表示源数组；startIndex 表示开始复制的起始索引，目标数组中将包含起始索引对应的元素，另外，startIndex 必须在 0 到 srcArray.length 之间；endIndex 表示终止索引，目标数组中将不包含终止索引对应的元素，endIndex 必须大于等于 startIndex，可以大于 srcArray.length，如果大于 srcArray.length，则目标数组中使用默认值填充。

注意：目标数组如果已经存在，将会被重构。

#### 例 2

假设有一个名称为 scores 的数组其元素为 8 个，现在需要定义一个名称为 newScores 的新数组。新数组的元素为 scores 数组的前 5 个元素，并且顺序不变。

使用 Arrays 类 copyOfRange() 方法完成数组复制的代码如下：

```
import java. util. Arrays;
public class Test20
{
    public static void main(String[] args)
    {
        //定义长度为 8 的数组
        int scores[]=new int[]{57,81,68,75,91,66,75,84};
        System.out.println("源数组内容如下：");

        //循环遍历源数组
        for(int i=0;i<scores.length;i++)
        {
            System.out.print(scores[i]+"\t");
        }

        //复制源数组的前 5 个元素到 newScores 数组中
        int newScores[]=(int[])Arrays.copyOfRange(scores,0,5);
        System.out.println("\n 复制的新数组内容如下：");

        //循环遍历目标数组，即复制后的新数组
        for(int j=0;j<newScores.length;j++)
        {
            System.out.print(newScores[j]+"\t");
        }
    }
```

}

在上述代码中，源数组 scores 中包含有 8 个元素，使用 Arrays.copyOfRange() 方法可以将该数组复制到长度为 5 的 newScores 数组中，截取 scores 数组的前 5 个元素即可。

该程序运行结果如下所示。

```
源数组内容如下：
57    81    68    75    91    66    75    84   
复制的新数组内容如下：
57    81    68    75    91
```

## 使用 arraycopy() 方法

arraycopy() 方法位于 java.lang.System 类中，其语法形式如下：

```
System.arraycopy(dataType[] srcArray,int srcIndex,int destArray,int destIndex,int length)
```

其中，srcArray 表示源数组；srcIndex 表示源数组中的起始索引；destArray 表示目标数组；destIndex 表示目标数组中的起始索引；length 表示要复制的数组长度。

使用此方法复制数组时，length+srcIndex 必须小于等于 srcArray.length，同时 length+destIndex 必须小于等于 destArray.length。

注意：目标数组必须已经存在，且不会被重构，相当于替换目标数组中的部分元素。

#### 例 3

假设在 scores 数组中保存了 8 名学生的成绩信息，现在需要复制该数组从第二个元素开始到结尾的所有元素到一个名称为 newScores 的数组中，长度为 12。scores 数组中的元素在 newScores 数组中从第三个元素开始排列。

使用 System.arraycopy() 方法来完成替换数组元素功能的代码如下：

```
import java.util.Arrays;
public class Test21
{
    public static void main(String[] args)
    {
        //定义源数组，长度为 8
        int scores[]=new int[]{100,81,68,75,91,66,75,100};

        //定义目标数组
        int newScores[]=new int[]{80,82,71,92,68,71,87,88,81,79,90,77};
        System.out.println("源数组中的内容如下：");

        //遍历源数组
        for(int i=0;i<scores.length;i++)
        {
            System.out.print(scores[i]+"\t");
        }
        System.out.println("\n 目标数组中的内容如下：");

        //遍历目标数组
        for(int j=0;j<newScores.length;j++)
        {
            System.out.print(newScores[j]+"\t");
        }
        System.arraycopy(scores,0,newScores,2,8);

        //复制源数组中的一部分到目标数组中
        System.out.println("\n 替换元素后的目标数组内容如下：");

        //循环遍历替换后的数组
        for(int k=0;k<newScores.length;k++)
        {
            System.out.print(newScores[k]+"\t");
        }
    }
}
```

在该程序中，首先定义了一个包含有 8 个元素的 scores 数组，接着又定义了一个包含有 12 个元素的 newScores 数组，然后使用 for 循环分别遍历这两个数组，输出数组中的元素。最后使用 System.arraycopy() 方法将 newScores 数组中从第三个元素开始往后的 8 个元素替换为 scores 数组中的 8 个元素值。

该程序运行的结果如下所示。

```
源数组中的内容如下：
100    81    68    75    91    66    75    100   
目标数组中的内容如下：
80    82    71    92    68    71    87    88    81    79    90    77   
替换元素后的目标数组内容如下：
80    82    100    81    68    75    91    66    75    100    90    77   
```

注意：在使用 arraycopy() 方法时要注意，此方法的命名违背了 Java 的命名惯例。即第二个单词 copy 的首字母没有大写，但按惯例写法应该为 arrayCopy。请读者在使用此方法时注意方法名的书写。

## 使用 clone() 方法

clone() 方法也可以实现复制数组。该方法是类 Object 中的方法，可以创建一个有单独内存空间的对象。因为数组也是一个 Object 类，因此也可以使用数组对象的 clone() 方法来复制数组。

clone() 方法的返回值是 Object 类型，要使用强制类型转换为适当的类型。其语法形式比较简单：

```
array_name.clone()
```

示例语句如下：

```
int[] targetArray=(int[])sourceArray.clone();
```

注意：目标数组如果已经存在，将会被重构。

#### 例 4

有一个长度为 8 的 scores 数组，因为程序需要，现在要定义一个名称为 newScores 的数组来容纳 scores 数组中的所有元素，可以使用 clone() 方法来将 scores 数组中的元素全部复制到 newScores 数组中。代码如下：

```
import java.util.Arrays;
public class Test22
{
    public static void main(String[] args)
    {
        //定义源数组，长度为 8
        int scores[]=new int[]{100,81,68,75,91,66,75,100};
        System.out.println("源数组中的内容如下：");

        //遍历源数组
        for(int i=0;i<scores.length;i++)
        {
            System.out.print(scores[i]+"\t");
        }

        //复制数组，将 Object 类型强制转换为 int[]类型
        int newScores[]=(int[])scores.clone();
        System.out.println("\n 目标数组内容如下：");

        //循环遍历目标数组
        for(int k=0;k<newScores.length;k++)
        {
            System.out.print(newScores[k]+"\t");
        }
    }
}
```

在该程序中，首先定义了一个长度为 8 的 scores 数组，并循环遍历该数组输出数组中的元素，然后定义了一个名称为 newScores 的新数组，并使用 scores.clone() 方法将 scores 数组中的元素复制给 newScores 数组。最后循环遍历 newScores 数组，输出数组元素。

程序运行结果如下所示。

```
源数组中的内容如下：
100    81    68    75    91    66    75    100   
目标数组内容如下：
100    81    68    75    91    66    75    100   
```

从运行的结果可以看出，scores 数组的元素与 newScores 数组的元素是相同的。