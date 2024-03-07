# Java 数组查找：数组查找指定元素（binarySearch()）

查找数组是指从数组中查询指定位置的元素，或者查询某元素在指定数组中的位置。使用 Arrays 类的 binarySearch() 方法可以实现数组的查找，该方法可使用二分搜索法来搜索指定数组，以获得指定对象，该方法返回要搜索元素的索引值。

binarySearch() 方法有多种重载形式来满足不同类型数组的查找需要，常用的重载形式有两种。

(1) 第一种形式如下：

```
binarySearch(Object[] a,Object key);
```

其中，a 表示要搜索的数组，key 表示要搜索的值。如果 key 包含在数组中，则返回搜索值的索引；否则返回 -1 或“-插入点”。插入点指搜索键将要插入数组的位置，即第一个大于此键的元素索引。

在进行数组查询之前，必须对数组进行排序（可以使用 sort() 方法）。如果没有对数组进行排序，则结果是不确定的。如果数组包含多个带有指定值的元素，则无法确认找到的是哪一个。

#### 例 1

声明 double 类型的 score 数组，接着调用 Arrays 类的 sort() 方法对 score 数组排序，排序后分别查找数组中值为 100 和 60 的元素，分别将结果保存到 index1 和 index2 变量中，最后输出变量的值。代码如下：

```
public static void main(String[] args)
{
    double[] score={99.5,100,98,97.5,100,95,85.5,100};
    Arrays.sort(score);
    int index1=Arrays.binarySearch(score,100);
    int index2=Arrays.binarySearch(score,60);
    System.out.println("查找到 100 的位置是："+index1);
    System.out.println("查找到 60 的位置是："+index2);
}
```

执行上述代码，输出结果如下：

```
查找到 100 的位置是：5
查找到 60 的位置是：-1
```

(2) 除了上述形式外，binarySearch() 还有另一种常用的形式，这种形式用于在指定的范围内查找某一元素。语法如下：

```
binarySearch(Object[] a,int fromIndex,int toIndex,Object key);
```

其中，a 表示要进行查找的数组，fromIndex 指定范围的开始处索引（包含开始处），toIndex 指定范围的结束处索引（不包含结束处），key 表示要搜索的元素。

在使用 binarySearch() 方法的上述重载形式时，也需要对数组进行排序，以便获取准确的索引值。如果要查找的元素 key 在指定的范围内，则返回搜索键的索引；否则返回 -1 或 “-插入点”。插入点指要将键插入数组的位置，即范围内第一个大于此键的元素索引。

#### 例 2

对例 1 中创建的 score 数组进行查找元素，指定开始位置为 2，结束位置为 6。代码如下:

```
public static void main(String[] args)
{
    double[] score={99.5,100,98,97.5,100,95,85.5,100};
    Arrays.sort(score);
    int index1=Arrays.binarySearch(score,2,6,100);
    int index2=Arrays.binarySearch(score,2,6,60);
    System.out.println("查找到 100 的位置是："+index1);
    System.out.println("查找到 60 的位置是："+ index2);
}
```

执行上述代码，输出结果如下：

```
查找到 100 的位置是：5
查找到 60 的位置是：-3
```

注意：实现对数组进行查找的方法很多，但是使用 Arrays 对象的 binarySearch() 方法是最简单、最方便的一种，因此该方法经常被应用。关于其他的查找方法这里不再演示，感兴趣的读者可以动手试一试。