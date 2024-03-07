# Java 多维数组

除了一维数组和二维数组外，Java 中还支持更多维的数组，如三维数组、四维数组和五维数组等，它们都属于多维数组。通常也将二维数组看作是多维数组。本文以三维数组为例来介绍多维数组。

三维数组有三个层次，可以将三维数组理解为一个一维数组，其内容的每个元素都是二维数组。依此类推，可以获取任意维数的数组。

多维数组的声明、初始化和使用都与二维数组相似，因此这里不再进行具体说明。

#### 例 1

假设程序中有一个名为 namelist 的 String 类型三维数组，下面编写代码对它进行遍历，输出每个元素的值。代码如下：

```
public static void main(String[] args)
{
    String[][][] namelist={{{"张阳","李风","陈飞"},{"乐乐","飞飞","小曼"}},
                            {{"Jack","Kimi"},{"Lucy","Lily","Rose"}},
                            {{"徐璐璐","陈海"},{"李丽丽","陈海清"}}};
    for(int i=0;i<namelist.length;i++)
    {
        for(int j=0;j<namelist[i].length;j++)
        {
            for(int k=0;k<namelist[i][j].length;k++)
            {
                System.out.println("namelist["+i+"]["+j+"]["+k+"]="+namelist[i][j][k]);
            }
        }
    }
}
```

执行上述代码，输出结果如下所示。

```
namelist[0][0][0]=张阳
namelist[0][0][1]=李风
namelist[0][0][2]=陈飞
namelist[0][1][0]=乐乐
namelist[0][1][1]=飞飞
namelist[0][1][2]=小曼
namelist[1][0][0]=Jack
namelist[1][0][1]=Kimi
namelist[1][1][0]=Lucy
namelist[1][1][1]=Lily
namelist[1][1][2]=Rose
namelist[2][0][0]=徐璐璐
namelist[2][0][1]=陈海
namelist[2][1][0]=李丽丽
namelist[2][1][1]=陈海清
```