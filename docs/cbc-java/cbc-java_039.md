# Java 输出杨辉三角形

本节利用过 Java 语言中的流程控制语句，如条件语句、循环语句和跳转语句等知识输出一个指定行数的杨辉三角形。

杨辉三角形由数字进行排列，可以把它看作是一个数字表，其基本特性是两侧数值均为 1，其他位置的数值是其左上方数值与右上角数值之和。打印杨辉三角形时需要使用到 for 循环语句。

打印杨辉三角形的实现思路是：每一行前面都是空格，而每行空格的个数需要根据总行数来确定，这个可以通过找规律归纳出来。关键是数值的实现，每一行的数值（除了第一列和最后一列）都是上一行两个数值之和，因此可以通过上一行来获取。

实现步骤如下：

(1) 创建名称为 Test28 的类，在该类中声明 num() 方法，在 num() 方法中传入两个参数，即 x 和 y。其中，x 表示行，y 表示列。num() 方法用于计算第 x 行第 y 列的数值。代码如下：

```
public static int num(int x,int y)
{
    if(y==1||y==x)
    {
         return 1;
    }
    int c=num(x-1,y-1)+num(x-1,y);
    return c;
}
```

(2) 创建名称为 calculate 的方法，在该方法中传入一个 int 类型的参数，该参数表示打印杨辉三角形的行数。代码如下：

```
public static void calculate(int row)
{
    for(int i=1;i<=row;i++)
    {
        for(int j=1;j<=row-i;j++)
        {
            System.out.print(" ");
        }
        for(int j=1;j<=i;j++)
        {   //打印空格后面的字符, 从第 1 列开始往后打印
            System.out.print(num(i,j) +" ");
        }
        System.out.println();
    }
}
```

(3) 在 main() 方法中添加代码，首先接收用户在控制台输入的打印行数，然后将行数作为参数传入到调用的 calculate() 方法中。代码如下：

```
public static void main(String[] args)
{
    Scanner scan=new Scanner(System.in);
    System.out.print("打印杨辉三角形的行数：");
    int row=scan.nextInt();
    calculate(row);
}
```

(4) 运行代码进行测试，其运行结果如下所示：

```
打印杨辉三角形的行数：7
      1
     1 1
    1 2 1
   1 3 3 1
  1 4 6 4 1
 1 5 10 10 5 1
1 6 15 20 15 6 1 
```