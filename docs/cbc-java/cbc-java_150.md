# Java Map 集合详解

Map 是一种键-值对（key-value）集合，Map 集合中的每一个元素都包含一个键对象和一个值对象。其中，键对象不允许重复，而值对象可以重复，并且值对象还可以是 Map 类型的，就像数组中的元素还可以是数组一样。

Map 接口主要有两个实现类：HashMap 类和 TreeMap 类。其中，HashMap 类按哈希算法来存取键对象，而 TreeMap 类可以对键对象进行排序。

Map 接口中提供的常用方法如表 1 所示。

表 1 Map 接口的常用方法

| 方法名称 | 说明 |
| V get(Object key) | 返回 Map 集合中指定键对象所对应的值。V 表示值的数据类型 |
| V put(K key, V value) | 向 Map 集合中添加键-值对，返回 key 以前对应的 value，如果没有， 则返回 null |
| V remove(Object key) | 从 Map 集合中删除 key 对应的键-值对，返回 key 对应的 value，如 果没有，则返回 null |
| Set entrySet() | 返回 Map 集合中所有键-值对的 Set 集合，此 Set 集合中元素的数据 类型为 Map.Entry |
| Set keySet() | 返回 Map 集合中所有键对象的 Set 集合 |

#### 例 1

每名学生都有属于自己的唯一编号，即学号。在毕业时需要将该学生的信息从系统中移除。

下面编写 Java 程序，使用 HashMap 来存储学生信息，其键为学生学号，值为姓名。毕业时，需要用户输入学生的学号，并根据学号进行删除操作。具体的实现代码如下：

```
import java.util.HashMap;
import java.util.Iterator;
import java.util.Scanner;
public class Test09
{
    public static void main(String[] args)
    {
        HashMap users=new HashMap();
        users.put("11","张浩太");    //将学生信息键值对存储到 Map 中
        users.put("22","刘思诚");
        users.put("33","王强文");
        users.put("44","李国量");
        users.put("55","王路路");
        System.out.println("******** 学生列表 ********");
        Iterator it=users.keySet().iterator();
        while(it.hasNext())
        {
            //遍历 Map
            Object key=it.next();
            Object val=users.get(key);
            System.out.println("学号："+key+"，姓名:"+val);
        }
        Scanner input=new Scanner(System.in);
        System.out.println("请输入要删除的学号：");
        int num=input.nextInt();
        if(users.containsKey(String.valueOf(num)))
        {    //判断是否包含指定键
            users.remove(String.valueOf(num));    //如果包含就删除
        }
        else
        {
            System.out.println("该学生不存在！");
        }
        System.out.println("******** 学生列表 ********");
        it=users.keySet().iterator();
        while(it.hasNext())
        {
            Object key=it.next();
            Object val=users.get(key);
            System.out.println("学号："+key+"，姓名："+val);
        }
    }
}
```

在该程序中，两次使用 while 循环遍历 HashMap 集合。当有学生毕业时，用户需要输入该学生的学号，根据学号使用 HashMap 类的 remove() 方法将对应的元素删除。程序运行结果如下所示。

```
******** 学生列表 ********
学号：44，姓名:李国量
学号：55，姓名:王路路
学号：22，姓名:刘思诚
学号：33，姓名:王强文
学号：11，姓名:张浩太
请输入要删除的学号：
22
******** 学生列表 ********
学号：44，姓名：李国量
学号：55，姓名：王路路
学号：33，姓名：王强文
学号：11，姓名：张浩太
```

```
******** 学生列表 ********
学号：44，姓名:李国量
学号：55，姓名:王路路
学号：22，姓名:刘思诚
学号：33，姓名:王强文
学号：11，姓名:张浩太
请输入要删除的学号：
44
******** 学生列表 ********
学号：55，姓名：王路路
学号：22，姓名：刘思诚
学号：33，姓名：王强文
学号：11，姓名：张浩太
```

注意：TreeMap 类的使用方法与 HashMap 类相同，唯一不同的是 TreeMap 类可以对键对象进行排序，这里不再赘述。