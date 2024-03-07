# Java 统计商品信息

在一个货架上有 5 件商品，编写程序，在输入商品价格之后输出最高价格、总价格和平均价格。

首先要创建一个包含 5 个空元素的价格数组，然后使用 for 循环使用户从控制台录入商品的价格，并将价格保存至数组中，再使用一个 for 循环来遍历该数组，求出最高价格和总价格。最后用总价格除以商品数量算出平均价格。

```
import java.util.Scanner;
public class Test07
{
    public static void main(String[] args)
    {
        //声明数组
        int[] prices=new int[5];
        int maxPrice=0,avgPrice=0,sumPrice=0;
        Scanner input=new Scanner(System.in);
        System.out.println("请输入 5 件商品的价格：");
        for(int i=0;i<5;i++)
        {
            prices[i]=input.nextInt();    //循环向数组中元素赋值
        }
        //计算价格最大值
        maxPrice=prices[0];    //假设最大值为第一个元素
        for(int index=1;index<prices.length;index++)
        {
            sumPrice+=prices[index];    // 汇总价格
            if(prices[index]>maxPrice)
            {
                maxPrice=prices[index];
            }
        }
        //平均价格=总价格/商品数量
        avgPrice=sumPrice/prices.length;
        System.out.println("本货架上商品的总价格为："+sumPrice+" 平均价格为："+avgPrice+" 最高价格为："+maxPrice);
    }
}
```

该程序运行后的结果如下所示。

```
请输入 5 件商品的价格：
88
64
44
62
79
本货架上商品的总价格为：249 平均价格为：49 最高价格为：88
```