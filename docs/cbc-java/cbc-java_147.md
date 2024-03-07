# Java Collection 接口

Collection 接口是 List 接口和 Set 接口的父接口，通常情况下不被直接使用。Collection 接口定义了一些通用的方法，通过这些方法可以实现对集合的基本操作。因为 List 接口和 Set 接口继承自 Collection 接口，所以也可以调用这些方法。

本节将介绍 Collection 接口中常用的方法，如表 1 所示。

表 1 Collection 接口的常用方法

| 方法名称 | 说明 |
| boolean add(E e) | 向集合中添加一个元素，E 是元素的数据类型 |
| boolean addAll(Collection c) | 向集合中添加集合 c 中的所有元素 |
| void clear() | 删除集合中的所有元素 |
| boolean contains(Object o) | 判断集合中是否存在指定元素 |
| boolean containsAll(Collection c) | 判断集合中是否包含集合 c 中的所有元素 |
| boolean isEmpty() | 判断集合是否为空 |
| Iterator<E>iterator() | 返回一个 Iterator 对象，用于遍历集合中的元素 |
| boolean remove(Object o) | 从集合中删除一个指定元素 |
| boolean removeAll(Collection c) | 从集合中删除所有在集合 c 中出现的元素 |
| boolean retainAll(Collection c) | 仅仅保留集合中所有在集合 c 中出现的元素 |
| int size() | 返回集合中元素的个数 |
| Object[] toArray() | 返回包含此集合中所有元素的数组 |

#### 例 1

经过前面的介绍，我们知道 Collection 是非常重要的一个接口，在表 1 中列出了其常用方法。本案例将编写一个简单的程序，演示如何使用 Collection 接口向集合中添加方法。具体实现代码如下：

```
public static void main(Strmg[] args)
{
    ArrayList list1=new ArrayList();    //创建集合 iist1
    ArrayList list2=new ArrayList();    //创建集合 Iist2
    list1.add("one");    //向 list1 添加一个元素
    list1.add("two");    //向 list1 添加一个元素
    list2.addAll(list1);    //将 list1 的所有元素添加到 list2
    list2.add("three");    //向 list2 添加一个元素
    System.out.println("list2 集合中的元素如下：");
    Iterator it1=list2.iterator();
    while(it1.hasNext())
    {
        System.out.print(it1.next()+"、");
    }
}
```

由于 Collection 是接口，不能对其实例化，所以上述代码中使用了 Collection 接口的 ArrayList 实现类来调用 Collection 的方法。add() 方法可以向 Collection 中添加一个元素，而调用 addAll() 方法可以将指定 Collection 中的所有元素添加到另一个 Collection 中。

代码创建了两个集合 list1 和 list2，然后调用 add() 方法向 list1 中添加了两个元素，再调用 addAll() 方法将这两个元素添加到 list2 中。接下来又向 list2 中添加了一个元素，最后输出 list2 集合中的所有元素，结果如下：

```
list2 集合中的元素如下：
one、two、three、
```

#### 例 2

创建一个案例，演示 Collection 集合中 size()、remove() 和 removeAll() 方法的应用。具体代码如下：

```
public static void main(String[] args)
{
    ArrayList list1=new ArrayList();    //创建集合 list1
    ArrayList list2=new ArrayList();    //创建集合 list2
    list1.add("one");
    list1.add("two");
    list1.add("three");
    System.out.println("list1 集合中的元素数量："+list1.size());    //输出 list1 中的元素数量
    list2.add("two");
    list2.add("four");
    list2.add("six");
    System.out.println("list2 集合中的元素数量："+list2.size());     //输出 list2 中的元素数量
    list2.remove(2);    //删除第 3 个元素
    System.out.println("\nremoveAll() 方法之后 list2 集合中的元素数量："+list2.size());
    System.out.println("list2 集合中的元素如下：");
    Iterator it1=list2.iterator();
    while(it1.hasNext())
    {
        System.out.print(it1.next()+"、");
    }
    list1.removeAll(list2);
    System.out.println("\nremoveAll() 方法之后 list1 集合中的元素数量："+list1.size());
    System.out.println("list1 集合中的元素如下：");
    Iterator it2=list1.iterator();
    while(it2.hasNext())
    {
        System.out.print(it2.next()+"、");
    }
}
```

list2 集合在调用 remove(2) 方法删除第 3 个元素之后剩下了 two 和 four。list1.removeAll(list2) 语句会从 list1 中将 list1 和 list2 中相同的元素删除，即删除 two 元素。最后输出结果如下：

```
list1 集合中的元素数量：3
Iist2 集合中的元素数量：3
remove() 方法之后 list2 集合中的元素数量：2
list2 集合中的元素如下：
two、four、
removeAH() 方法之后 list1 集合中的元素数量：2
list1 集合中的元素如下：
one、 three、
```

注意：retainAll() 方法的作用与 removeAll() 方法相反，即保留两个集合中相同的元素，其他全部删除。