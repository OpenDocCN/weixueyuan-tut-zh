# Java 商品信息查询

综合一维数组和二维数组的相关知识，以及数组排序的多种算法来实现商品信息查询的功能。

假设在仓库系统中，每件商品都有 3 个库存信息，分别是入库量、出库量和当前库存量。定义一个一维数组来存储 5 件商品的名称，并定义一个二维数组来存储这 5 件商品的 3 个库存信息。用户可以根据商品名称查询该商品的所有库存，也可以查看某个类别库存下数量小于 100 的商品名单，并将该类别的所有库存量按从低到高的顺序排列。

具体的代码如下：

```
import java.util.Arrays;
import java.util.Scanner;
public class Test28
{
    public static void main(String[] args)
    {
        Scanner input=new Scanner(System.in);
        String[] products={"洗发水","纸巾","水杯","牙膏","香皂"};
        int[][] amounts={{50,80,90},{40,80,78},{50,45,789},{100,685,55},{898,754,63},{99,478,685}};
        System.out.println("*************** 库存系统 ***************");
        System.out.println("请输入要查询库存信息的商品名称：");
        String name=input.next();
        for(int i=0;i<products.length;i++)
        {
            if(products[i].equals(name))
            {
                System.out.println("商品【"+products[i]+"】的库存信息如下：");
                System.out.println("入库 \t 出库 \t 库存");
                for(int j=0;j<3;j++)
                {
                    System.out.print(amounts[i][j]+"\t");
                }
                break;
            }
        }
        System.out.println("\n*************** 查询库存不足 100 的商品 ***************");
        System.out.println("1.入库 \t2.出库 \t3.库存");
        System.out.println("请输人序号：");
        int no=input.nextInt();
        int[] temp=new int[5];    //定义数组，存储该类别的所有商品
        System.out.println("该类别下数量较少的商品有：");
        for(int i=0;i<5;i++)
        {
            temp[i]=amounts[i][no-1];    //将指定类别的所有商品名称存储到 temp 数组中
            if(amounts[i][no-1]<60)
            {
                System.out.print(products[i]+"\t");
            }
        }
        //使用冒泡排序，将商品的库存量以从低到高的顺序排列
        for(int i=1;i<temp.length;i++)
        {
            for(int j=0;j<temp.length-i;j++)
            {
                if(temp[j]>temp[j+1])
                {
                    int x=temp[j];
                    temp[j]=temp[j+1];
                    temp[j+1]=x;
                }
            }
        }
        System.out.println("\n 该类别的商品库存信息从低到高的排列如下：");
        for(int i=0;i<temp.length;i++)
        {
            System.out.print(temp[i]+"\t");
        }
    }
}
```

在本案例中，分别定义了一个一维数组和一个二维数组，用于存储商品的名称和对应的 3 个库存信息。接着根据名称可以查看该商品的库存信息，也可以查找某个库存中数量小于 100 的商品名称。最后，对指定的库存进行冒泡排序并输出。

运行程序，执行结果如下所示。

```
*************** 库存系统 ***************
请输入要查询库存信息的商品名称：
水杯 商品【水杯】的库存信息如下
入库 出库 库存
50   45   789
*************** 查询库存不足 100 的商品 ***************
1.入库 2.出库 3.库存 请输人序号：
1
该类别下数量较少的商品有：
洗发水 纸巾 水杯
该类别的商品库存信息从低到高的排列如下：
40 50 50 100 898 
```