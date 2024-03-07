# Java Collection 类：sort()升序排序、reverse()降序排序、copy()复制、fill()填充

Collections 类提供了许多操作集合的静态方法，借助这些静态方法可以实现集合元素的排序、填充和复制等操作。下面介绍 Collections 类中操作集合的常用方法。

## 正向排序

使用 Collections 类的静态方法 sort() 可以对集合中的元素进行升序排序。这要求列表中的所有元素都必须实现 Comparable 接口，而且所有元素都必须是使用指定比较器可相互比较的。

sort() 方法主要有如下两种重载形式。

1.  void sort(List list)：根据元素的自然顺序对集合中的元素进行升序排序。
2.  void sort(List list,Comparator comparator)：按 comparator 参数指定的排序方式对集合中的元素进行排序。

#### 例 1

编写一个程序，对用户输入的 5 个商品价格进行排序后输出。这里要求使用 Collections 类中 sort() 方法按从低到局的顺序对其进行排序，最后将排序后的成绩输出。

具体实现代码如下：

```
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Scanner;
public class Test10
{
    public static void main(String[] args)
    {
        Scanner input=new Scanner(System.in);
        List prices=new ArrayList();
        for(int i=0;i<5;i++)
        {
            System.out.println("请输入第 "+(i+1)+" 个商品的价格：");
            int p=input.nextInt();
            prices.add(Integer.valueOf(p));    //将录入的价格保存到 List 集合中
        }
        Collections.sort(prices);    //调用 sort()方法对集合进行排序
        System.out.println("价格从低到高的排列为：");
        for(int i=0;i<prices.size();i++)
        {
            System.out.print(prices.get(i)+"\t");
        }
    }
}
```

如上述代码，循环录入 5 个价格，并将每个价格都存储到已定义好的 List 集合 prices 中，然后使用 Collections 类的 sort() 方法对该集合元素进行升序排序。最后使用 for 循环遍历 users 集合，输出该集合中的元素。

该程序的执行结果如下所示。

```
请输入第 1 个商品的价格：
85
请输入第 2 个商品的价格：
48
请输入第 3 个商品的价格：
66
请输入第 4 个商品的价格：
80
请输入第 5 个商品的价格：
18
价格从低到高的排列为：
18    48    66    80    85
```

## 逆向排序

与 sort() 方法的作用相反，调用 reverse() 静态方法可以对指定集合元素进行逆向排序。该方法的定义如下：

```
void reverse(List list)    //对集合中的元素进行反转排序
```

#### 例 2

循环录入 5 个商品的名称，并按录入时间的先后顺序进行降序排序，即后录入的先输出。

下面编写程序，使用 Collections 类的 reverse() 方法对保存到 List 集合中的 5 个商品名称进行反转排序，并输出排序后的商品信息。具体的实现代码如下：

```
import java.util.ArrayList;
import java.util.Coliections;
import java.util.List;
import java.util.Scanner;
public class Test11
{
    public static void main(String[] args)
    {
        Scanner input=new Scanner(System.in);
        List students=new ArrayList();
        System.out.println("******** 商品信息 ********");
        for(int i=0;i<5;i++)
        {
            System.out.println("请输入第 "+(i+1)+" 个商品的名称：");
            String name=input.next();
            students.add(name);    //将录入的商品名称存到 List 集合中
        }
        Collections.reverse(students);    //调用 reverse()方法对集合元素进行反转排序
        System.out.println("按录入时间的先后顺序进行降序排列为：");
        for(int i=0;i<5;i++)
        {
            System.out.print(students.get(i)+"\t");
        }
    }
}
```

如上述代码，首先循环录入 5 个商品的名称，并将这些名称保存到 List 集合中，然后调用 Collections 类中的 reverse() 方法对该集合元素进行反转排序。最后使用 for 循环将排序后的集合元素输出。

执行该程序，输出结果如下所示。

```
******** 商品信息 ********
请输入第 1 个商品的名称：
果粒橙
请输入第 2 个商品的名称：
冰红茶
请输入第 3 个商品的名称：
矿泉水
请输入第 4 个商品的名称：
软面包
请输入第 5 个商品的名称：
巧克力
按录入时间的先后顺序进行降序排列为：
巧克力    软面包    矿泉水    冰红茶    果粒橙   
```

## 复制

Collections 类的 copy() 静态方法用于将指定集合中的所有元素复制到另一个集合中。执行 copy() 方法后，目标集合中每个已复制元素的索引将等同于源集合中该元素的索引。

copy() 方法的语法格式如下：

```
void copy(List <? super T> dest,List<? extends T> src)
```

其中，dest 表示目标集合对象，src 表示源集合对象。

注意：目标集合的长度至少和源集合的长度相同，如果目标集合的长度更长，则不影响目标集合中的其余元素。如果目标集合长度不够而无法包含整个源集合元素，程序将抛出 IndexOutOfBoundsException 异常。

#### 例 3

在一个集合中保存了 5 个商品名称，现在要使用 Collections 类中的 copy() 方法将其中的 3 个替换掉。具体实现的代码如下：

```
public class Test12
{
    public static void main(String[] args)
    {
        Scanner input=new Scanner(System.in);
        List srcList=new ArrayList();
        List destList=new ArrayList();
        destList.add("苏打水");
        destList.add("木糖醇");
        destList.add("方便面");
        destList.add("火腿肠");
        destList.add("冰红茶");
        System.out.println("原有商品如下：");
        for(int i=0;i<destList.size();i++)
        {
            System.out.println(destList.get(i));
        }
        System.out.println("输入替换的商品名称：");
        for(int i=0;i<3;i++)
        {
            System.out.println("第 "+(i+1)+" 个商品：");
            String name=input.next();
            srcList.add(name);
        }
        //调用 copy()方法将当前商品信息复制到原有商品信息集合中
        Collections.copy(destList,srcList);
        System.out.println("当前商品有：");
        for(int i=0;i<destList.size();i++)
        {
            System.out.print(destList.get(i)+"\t");
        }
    }
}
```

如上述代码，首先创建了两个 List 对象 srcList 和 destList，并向 destList 集合中添加了 5 个元素，向 srcList 集合中添加了 3 个元素，然后调用 Collections 类中 copy() 方法将 srcList 集合中的全部元素复制到 destList 集合中。由于 destList 集合中含有 5 个元素，故最后两个元素不会被覆盖。

运行该程序，具体的执行结果如下所示。

```
原有商品如下：
苏打水
木糖醇
方便面
火腿肠
冰红茶
输入替换的商品名称：
第 1 个商品：
燕麦片
第 2 个商品：
八宝粥
第 3 个商品：
软面包
当前商品有：
燕麦片    八宝粥软面包    火腿肠    冰红茶
```

## 填充

Collections 类的 fill() 静态方法可以对指定集合的所有元素进行填充操作。fill() 方法的定义如下:

```
void fill(List<? super T> list,T obj)    //使用指定元素替换指定列表中的所有元素
```

其中，list 表示要替换的集合对象，obj 表示用来替换指定集合的元素值。

#### 例 4

编写一个程序，要求用户输入 3 个商品名称，然后使用 Collections 类中的 fill() 方法对商品信息进行重置操作，即将所有名称都更改为“未填写”。具体的实现代码如下：

```
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Scanner;
public class Test13
{
    public static void main(String[] args)
    {
        Scanner input=new Scanner(System.in);
        List products=new ArrayList();
        System.out.println("******** 商品信息 ********");
        for(int i=0;i<3;i++)
        {
            System.out.println("请输入第 "+(i+1)+" 个商品的名称：");
            String name=input.next();
            products.add(name);    //将用户录入的商品名称保存到 List 集合中
        }
        System.out.println("重置商品信息，将所有名称都更改为'未填写'");
        Collections.fill(products,"未填写");
        System.out.println("重置后的商品信息为：");
        for(int i=0;i<products.size();i++)
        {
            System.out.print(products.get(i)+"\t");
        }
    }
}
```

如上述代码，首先循环录入 3 个商品名称，并将这些商品信息存储到 List 集合中，然后调用 Collections 类中的 fill() 方法将该集合中的所有元素值替换为“未填写”。最后使用 for 循环将替换后的集合元素输出。

运行该程序，执行结果如下所示。

```
******** 商品信息 ********
请输入第 1 个商品的名称：
苏打水
请输入第 2 个商品的名称：
矿泉水
请输入第 3 个商品的名称：
冰红茶
重置商品信息，将所有名称都更改为'未填写'
重置后的商品信息为：
未填写    未填写    未填写    
```