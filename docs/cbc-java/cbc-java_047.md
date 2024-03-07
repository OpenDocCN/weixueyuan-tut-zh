# Java 分割字符串（spilt()）

String 类的 split() 方法可以按指定的分割符对目标字符串进行分割，分割后的内容存放在字符串数组中。该方法主要有如下两种重载形式：

```
str.split(String sign)
str.split(String sign,int limit)
```

其中，str 为需要分割的目标字符串；sign 为指定的分割符，可以是任意字符串；limit 表示分割后生成的字符串的限制个数，如果不指定，则表示不限制，直到将整个目标字符串完全分割为止。

#### 例 1

使用 split() 方法对字符串进行分割的实例如下：

```
public static void main(String[] args)
{
    String Colors="Red,Black,White,Yellow,Blue";
    String[] arr1=Colors.split(",");    //不限制元素个数
    String[] arr2=Colors.split(",",3);    //限制元素个数为 3
    System.out.println("所有颜色为：");
    for(int i=0;i<arr1.length;i++)
    {
        System.out.println(arr1[i]);
    }
    System.out.println("前三个颜色为：");
    for(int j=0;j<arr2.length;j++)
    {
        System.out.println(arr2[j]);
    }
}
```

输出结果如下：

```
所有颜色为：
Red
Black
White
Yellow Blue
前三个颜色为：
Red
Black
White,Yellow,Blue
```

从输出的结果可以看出，当指定分割字符串后组成的数组长度（大于或等于 1）时，数组的前几个元素为字符串分割后的前几个字符，而最后一个元素为字符串的剩余部分。

例如，在该实例中，指定了 arr2 的长度为 3，而字符串 Colors 分割后组成的数组长度为 5。因此会将 arr2 中的前两个元素赋值为 Colors 分割后的前两个字符，arr2 中的第 3 个元素为 Colors 字符串的后 3 个字符组成的字符串。