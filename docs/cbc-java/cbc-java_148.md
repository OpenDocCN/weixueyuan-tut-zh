# Java List 集合：ArrayList 类、LinkList 类、ArrayList 类和 LinkList 类的区别

List 接口实现了 Collection 接口，它主要有两个实现类：ArrayList 类和 LinkedList 类。在 List 集合中允许出现重复元素。与 Set 集合不同的是，在 List 集合中的元素是有序的，可以根据索引位置来检索 List 集合中的元素，第一个添加到 List 集合中的元素的索引为 0，第二个为 1，依此类推。

## ArrayList 类

ArrayList 类提供了快速的基于索引的成员访问方式，对尾部成员的增加和删除支持较好。使用 ArrayList 创建的集合，允许对集合中的元素进行快速的随机访问，不过，向 ArrayList 中插入与删除元素的速度相对较慢。该类的常用构造方法有如下两种重载形式。

*   ArrayList()：构造一个初始容量为 10 的空列表。
*   ArrayList(Collection<?extends E>c)：构造一个包含指定 Collection 的元素的列表，这些元素是按照该 Collection 的迭代器返回它们的顺序排列的。

ArrayList 类除了包含 Collection 接口中的所有方法之外，还包括 List 接口中提供的如表 1 所示的方法。

表 1 ArrayList 类的常用方法

| 方法名称 | 说明 |
| E get(int index) | 获取此集合中指定索引位置的元素，E 为集合中元素的数据类型 |
| int index(Object o) | 返回此集合中第一次出现指定元素的索引，如果此集合不包含该元 素，则返回 -1 |
| int lastIndexOf(Obj ect o) | 返回此集合中最后一次出现指定元素的索引，如果此集合不包含该 元素，则返回 -1 |
| E set(int index, E element) | 将此集合中指定索引位置的元素修改为 element 参数指定的对象。 此方法返回此集合中指定索引位置的原元素 |
| List<E> subList(int fromlndex, int tolndex) | 返回一个新的集合，新集合中包含 fromlndex 和 tolndex 索引之间 的所有元素。包含 fromlndex 处的元素，不包含 tolndex 索引处的
元素 |

#### 例 1

使用 ArrayList 类向集合中添加三个商品信息，包括商品编号、名称和价格，然后遍历集合输出这些商品信息。

(1) 创建一个商品类 Product，在该类中定义 3 个属性和 toString() 方法，分别实现 setter/getter 方法。代码的实现如下：

```
public class Product
{   
    //商品类
    private int id;    //商品编号
    private String name;    //名称
    private float price;    //价格
    public Product(int id,String name,float price)
    {
        this.name=name;
        this.id=id;
        this.price=price;
    }
    //这里是上面 3 个属性的 setter/getter 方法，这里省略
    public String toString()
    {
        return"商品编号："+id+"，名称:"+name+"，价格："+price;
    }
}
```

(2) 创建一个测试类，调用 Product 类的构造函数实例化三个对象，并将 Product 对象保存至 ArrayList 集合中。最后遍历该集合，输出商品信息。测试类的代码实现如下：

```
import Java.util.ArrayList;
import java.util.List;
public class Test03
{
    public static void main(String[] args)
    {
        Product pd1=new Product(4,"木糖醇",10);
        Product pd2=new Product(5,"洗发水",12);
        Product pd3=new Product(3,"热水壶",49);
        List list=new ArrayList();    //创建集合
        list.add(pd1);
        list.add(pd2);
        list.add(pd3);
        System.out.println("*************** 商品信息 ***************");
        for(int i=0;i<list.size();i++)
        {
            //循环遍历集合，输出集合元素
            Product product=(Product)list.get(i);
            System.out.println(product);
        }
    }
}
```

该示例中的 ArrayList 集合中存放的是自定义类 Product 的对象，这与存储的 String 类的对象是相同的。与 Set 不同的是，List 集合中存在 get() 方法，该方法可以通过索引来获取所对应的值，获取的值为 Object 类，因此需要将该值转换为 Product 类，从而获取商品信息。

该程序的运行结果如下所示。

```
*************** 商品信息 ***************
商品编号：4，名称:木糖醇，价格：10.0
商品编号：5，名称:洗发水，价格：12.0
商品编号：3，名称:热水壶，价格：49.0
```

#### 例 2

在使用 List 集合时需要注意区分 indexOf() 方法和 lastIndexOf() 方法。前者是获得指定对象的最小索引位置，而后者是获得指定对象的最大索引位置。前提条件是指定的对象在 List 集合中有重复的对象，否则这两个方法获取的索引值相同。

下面的案例代码演示了 indexOf() 方法和 lastIndexOf() 方法的区别。

```
public static void main(String[] args)
{
    List list=new ArrayList();
    list.add("One");
    list.add("|");
    list.add("Two");
    list.add("|");
    list.add("Three");
    list.add("|");
    list.add("Four");
    System.out.println("list 集合中的元素数量："+list.size());
    System.out.println("list 集合中的元素如下：");
    Iterator it=list.iterator();
    while(it.hasNext())
    {
        System.out.print(it.next()+"、");
    }
    System.out.println("\n 在 list 集合中'丨'第一次出现的位置是："+list.indexOf("|"));
    System.out.println("在 list 集合中'丨'最后一次出现的位置是："+list.lastIndexOf("|"));
}
```

上述代码创建一个 List 集合 list，然后添加了 7 个元素，由于索引从 0 开始，所以最后一个元素的索引为 6。输出结果如下：

```
list 集合中的元素数量：7
list 集合中的元素如下：
One、|、Two、|、Three、|、Four、
在 list 集合中'|'第一次出现的位置是：1
在 list 集合中'|'最后一次出现的位置是：5
```

#### 例 3

使用 subList() 方法截取 List 集合中部分元素时要注意，新的集合中包含起始索引位置的元素，但是不包含结束索引位置的元素。例如，subList(1,4) 方法实际截取的是索引 1 到索引 3 的元素，并组成新的 List 集合。

下面的案例代码演示了 subList() 方法的具体用法。

```
public static void main(String[] args)
{
    List list=new ArrayList();
    list.add("One");
    list.add("Two");
    list.add("Three");
    list.add("Four");
    list.add("Five");
    list.add("Six");
    list.add("Seven");
    System.out.println("list 集合中的元素数量："+list.size());
    System.out.println("list 集合中的元素如下：");
    Iterator it=list.iterator();
    while(it.hasNext())
    {
        System.out.print(it.next()+"、");
    }
    List sublist=new ArrayList();
    sublist=list.subList(2,5);    //从 list 集合中截取索引 2~5 的元素，保存到 sublist 集合中
    System.out.println("\nsublist 集合中元素数量："+sublist.size());
    System.out.println("sublist 集合中的元素如下：");
    it=sublist.iterator();
    while(it.hasNext())
    {
        System.out.print(it.next()+"、");
    }
}
```

输出结果如下：

```
list 集合中的元素数量：7
list 集合中的元素如下：
One、Two、Three、Four、Five、Six、Seven、
sublist 集合中元素数量：3
sublist 集合中的元素如下：
Three、Four、Five、
```

## LinkList 类

LinkedList 类采用链表结构保存对象，这种结构的优点是便于向集合中插入或者删除元素。需要频繁向集合中插入和删除元素时，使用 LinkedList 类比 ArrayList 类效果高，但是 LinkedList 类随机访问元素的速度则相对较慢。这里的随机访问是指检索集合中特定索引位置的元素。

LinkedList 类除了包含 Connection 接口和 List 接口中的所有方法之外，还特别提供了表 2 所示的方法。

表 2 LinkList 类中的方法

| 方法名称 | 说明 |
| void addFirst(E e) | 将指定元素添加到此集合的开头 |
| void addLast(E e) | 将指定元素添加到此集合的末尾 |
| E getFirst() | 返回此集合的第一个元素 |
| E getLast() | 返回此集合的最后一个元素 |
| E removeFirst() | 删除此集合中的第一个元素 |
| E removeLast() | 删除此集合中的最后一个元素 |

#### 例 4

在仓库管理系统中要记录入库的商品名称，并且需要输出第一个录入的商品名称和最后—个商品名称。下面使用 LinkedList 集合来完成这些功能，实现代码如下：

```
public static void main(String[] args)
{
    LinkedList<String> products=new LinkedList<String>();    //创建集合对象
    String p1=new String("六角螺母");
    String p2=new String("10A 电缆线");
    String p3=new String("5M 卷尺");
    String p4=new String("4CM 原木方板");
    products.add(p1);    //将 pi 对象添加到 LinkedList 集合中
    products.add(p2);    //将 p2 对象添加到 LinkedList 集合中
    products.add(p3);    //将 p3 对象添加到 LinkedList 集合中
    products.add(p4);    //将 p4 对象添加到 LinkedList 集合中
    String p5=new String("标准文件夹小柜");
    products.addLast(p5);    //向集合的末尾添加 p5 对象
    System.out.print("*************** 商品信息 ***************");
    System.out.println("\n 目前商品有：");
    for(int i=0;i<products.size();i++)
    {
        System.out.print(products.get(i)+"\t");
    }
    System.out.println("\n 第一个商品的名称为："+products.getFirst());
    System.out.println("最后一个商品的名称为："+products.getLast());
    products.removeLast();    //删除最后一个元素
    System.out.println("删除最后的元素，目前商品有：");
    for(int i=0;i<products.size();i++)
    {
        System.out.print(products.get(i)+"\t");
    }
}
```

如上述代码，首先创建了 5 个 String 对象，分别为 p1、p2、p3、p4 和 p5。同时将 pl、 p2、p3 和 p4 对象使用 add() 方法添加到 LinkedList 集合中，使用 addLast() 方法将 p5 对象添加到 LinkedList 集合中。分别调用 LinkedList 类中的 getFirst() 方法和 getLast()方法获取第一个和最后一个商品名称。最后使用 removeLast() 方法将最后一个商品信息删除，并将剩余商品信息打印出来。

LinkedList<String> 中的 <String> 是 Java 中的泛型，用于指定集合中元素的数据类型，例如这里指定元素类型为 String，则该集合中不能添加非 String 类型的元素。

运行程序，执行结果如下：

```
*************** 商品信息 ***************
目前商品有：
六角螺母    10A 电缆线    5M 卷尺    4CM 原木方板    标准文件夹小柜   
第一个商品的名称为：六角螺母
最后一个商品的名称为：标准文件夹小柜
删除最后的元素，目前商品有：
六角螺母    10A 电缆线    5M 卷尺    4CM 原木方板
```