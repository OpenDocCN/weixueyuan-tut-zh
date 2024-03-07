# Java 枚举（enum）详解：Java 声明枚举类型、枚举（enum）类、EnumMap 与 EnumSet

枚举是一个被命名的整型常数的集合，用于声明一组带标识符的常数。枚举在曰常生活中很常见，例如一个人的性别只能是“男”或者“女”，一周的星期只能是 7 天中的一个等。类似这种当一个变量有几种固定可能的取值时，就可以将它定义为枚举类型。

在 JDK 1.5 之前没有枚举类型，那时候一般用接口常量来替代。而使用 Java 枚举类型 enum 可以更贴近地表示这种常量。

## 声明枚举

声明枚举时必须使用 enum 关键字，然后定义枚举的名称、可访问性、基础类型和成员等。枚举声明的语法如下：

```
enum-modifiers enum enumname:enum-base
{
    enum-body,
}
```

其中，enum-modifiers 表示枚举的修饰符主要包括 public、private 和 internal；enumname 表示声明的枚举名称；enum-base 表示基础类型；enum-body 表示枚举的成员，它是枚举类型的命名常数。

任意两个枚举成员不能具有相同的名称，且它的常数值必须在该枚举的基础类型的范围之内，多个枚举成员之间使用逗号分隔。

**提示：如果没有显式地声明基础类型的枚举，那么意味着它所对应的基础类型是 int。**

#### 例 1

下面代码定义了一个表示性别的枚举类型 SexEnum 和一个表示颜色的枚举类型 Color。

```
public enum SexEnum
{
    male,female;
}
public enum Color
{
    RED,BLUE,GREEN,BLACK;
}
```

之后便可以通过枚举类型名直接引用常量，如 SexEnum.male、Color.RED。

使用枚举还可以使 switch 语句的可读性更强，例如以下示例代码：

```
enum Signal
{
    //定义一个枚举类型
    GREEN,YELLOW,RED
}
public class TrafficLight
{
    Signal color=Signal.RED;
    public void change()
    {
        switch(color)
        {
            case RED:
                color=Signal.GREEN;
                break;
            case YELLOW:
                color=Signal.RED;
                break;
            case GREEN:
                color=Signal.YELLOW;
                break;
        }
    }
}
```

## 枚举类

Java 中的每一个枚举都继承自 java.lang.Enum 类。当定义一个枚举类型时，每一个枚举类型成员都可以看作是 Enum 类的实例，这些枚举成员默认都被 final、public, static 修饰，当使用枚举类型成员时，直接使用枚举名称调用成员即可。

所有枚举实例都可以调用 Enum 类的方法，常用方法如表 1 所示。

表 1 Enum 类的常用方法

| 方法名称 | 描述 |
| values() | 以数组形式返回枚举类型的所有成员 |
| valueOf() | 将普通字符串转换为枚举实例 |
| compareTo() | 比较两个枚举成员在定义时的顺序 |
| ordinal() | 获取枚举成员的索引位置 |

#### 例 2

通过调用枚举类型实例的 `values() 方法`可以将枚举的所有成员以数组形式返回，也可以通过该方法获取枚举类型的成员。

下面的示例创建一个包含 3 个成员的枚举类型 Signal，然后调用 values() 方法输出这些成员。

```
enum Signal
{
    //定义一个枚举类型
    GREEN,YELLOW,RED;
}
public static void main(String[] args)
{
    for(int i=0;i<Signal.values().length;i++)
    {
        System.out.println("枚举成员："+Signal.values()[i]);
    }
}
```

输出结果如下：

```
枚举成员：GREEN
枚举成员：YELLOW
枚举成员：RED
```

#### 例 3

创建一个示例，调用 `valueOf() 方法`获取枚举的一个成员，再调用 compareTo() 方法进行比较，并输出结果。具体实现代码如下：

```
public class TestEnum
{
    public enum Sex
    {
        //定义一个枚举
        male,female;
    }
    public static void main(String[] args)
    {
        compare(Sex.valueOf("male"));    //比较
    }
    public static void compare(Sex s)
    {
        for(int i=0;i<Sex.values().length;i++)
        {
            System.out.println(s+"与"+Sex.values()[i]+"的比较结果是："+s.compareTo(Sex.values()[i]));
        }
    }
}
```

上述代码中使用 Sex.valueOf("male") 取出枚举成员 male 对应的值，再将该值与其他枚举成员进行比较。最终输出结果如下：

```
male 与 male 的比较结果是：0
male 与 female 的比较结果是：-1
```

#### 例 4

通过调用枚举类型实例的 `ordinal() 方法`可以获取一个成员在枚举中的索引位置。下面的示例创建一个包含 3 个成员的枚举类型 Signal，然后调用 ordinal() 方法输出成员及对应索引位置。

具体实现代码如下：

```
public class TestEnum1
{
    enum Signal
    {
        //定义一个枚举类型
        GREEN,YELLOW,RED;
    }
    public static void main(String[] args)
    {
        for(int i=0;i<Signal.values().length;i++)
        {
            System.out.println("索引"+Signal.values()[i].ordinal()+"，值："+Signal.values()[i]);
        }
    }
}
```

输出结果如下：

```
索引 0，值：GREEN
索引 1，值：YELLOW
索引 2，值：RED
```

## 为枚举添加方法

Java 为枚举类型提供了一些内置的方法，同时枚举常量也可以有自己的方法。此时要注意必须在枚举实例的最后一个成员后添加分号，而且必须先定义枚举实例。

#### 例 5

下面的代码创建了一个枚举类型 WeekDay，而且在该类型中添加了自定义的方法。

```
enum WeekDay
{
    Mon("Monday"),Tue("Tuesday"),Wed("Wednesday"),Thu("Thursday"),Fri("Friday"),Sat("Saturday"),Sun("Sunday");
    //以上是枚举的成员，必须先定义，而且使用分号结束
    private final String day;
    private WeekDay(String day)
    {
        this.day=day;
    }
    public static void printDay(int i)
    {
        switch(i)
        {
            case 1:
                System.out.println(WeekDay.Mon);
                break;
            case 2:
                System.out.println(WeekDay.Tue);
                break;
            case 3:
                System.out.println(WeekDay.Wed);
                break;
            case 4:
                System.out.println(WeekDay.Thu);
                break;
            case 5:
                System.out.println(WeekDay.Fri);
                break;
            case 6:
                System.out.println(WeekDay.Sat);
                break;
            case 7:
                System.out.println(WeekDay.Sun);
                break;
            default:
                System.out.println("wrong number!");
        }
    }
    public String getDay()
    {
        return day;
    }
}
```

上面代码创建了 WeekDay 枚举类型，下面遍历该枚举中的所有成员，并调用 printDay() 方法。示例代码如下：

```
public static void main(String[] args)
{
    for(WeekDay day:WeekDay.values())
    {
        System.out.println(day+"====>"+day.getDay());
    }
    WeekDay.printDay(5);
}
```

输出结果如下：

```
Mon====>Monday
Tue====>Tuesday
Wed====>Wednesday
Thu====>Thursday
Fri====>Friday
Sat====>Saturday
Sun====>Sunday
Fri
```

Java 中的 enum 还可以跟 Class 类一样覆盖基类的方法。下面示例代码创建的 Color 枚举类型覆盖了 toString() 方法。

```
public class Test
{
    public enum Color
    {
        RED("红色",1),GREEN("绿色",2),WHITE("白色",3),YELLOW("黄色",4);
        //成员变量
        private String name;
        private int index;
        //构造方法
        private Color(String name,int index)
        {
            this.name=name;
            this.index=index;
        }
        //覆盖方法
        @Override
        public String toString()
        {
            return this.index+"-"+this.name;
        }
    }
    public static void main(String[] args)
    {
        System.out.println(Color.RED.toString());    //输出：1-红色
    }
}
```

## EnumMap 与 EnumSet

为了更好地支持枚举类型，java.util 中添加了两个新类：`EnumMap` 和 `EnumSet`。使用它们可以更高效地操作枚举类型。

#### EnumMap 类

EnumMap 是专门为枚举类型量身定做的 Map 实现。虽然使用其他的 Map（如 [HashMap](http://c-local.biancheng.net/view/1067.html)）实现也能完成枚举类型实例到值的映射，但是使用 EnumMap 会更加高效。

HashMap 只能接收同一枚举类型的实例作为键值，并且由于枚举类型实例的数量相对固定并且有限，所以 EnumMap 使用数组来存放与枚举类型对应的值，使得 EnumMap 的效率非常高。

#### 例 6

下面是使用 EnumMap 的一个代码示例。枚举类型 DataBaseType 里存放了现在支持的所有数据库类型。针对不同的数据库，一些数据库相关的方法需要返回不一样的值，例如示例中 getURL() 方法。

```
//定义数据库类型枚举
public enum DataBaseType
{
    MYSQUORACLE,DB2,SQLSERVER
}
//某类中定义的获取数据库 URL 的方法以及 EnumMap 的声明
private EnumMap<DataBaseType,String>urls=new EnumMap<DataBaseType,String>(DataBaseType.class);
public DataBaseInfo()
{
    urls.put(DataBaseType.DB2,"jdbc:db2://localhost:5000/sample");
    urls.put(DataBaseType.MYSQL,"jdbc:mysql://localhost/mydb");
    urls.put(DataBaseType.ORACLE,"jdbc:oracle:thin:@localhost:1521:sample");
    urls.put(DataBaseType.SQLSERVER,"jdbc:microsoft:sqlserver://sql:1433;Database=mydb");
}
//根据不同的数据库类型，返回对应的 URL
//@param type DataBaseType 枚举类新实例
//@return
public String getURL(DataBaseType type)
{
    return this.urls.get(type);
}
```

在实际使用中，EnumMap 对象 urls 往往是由外部负责整个应用初始化的代码来填充的。这里为了演示方便，类自己做了内容填充。

从本例中可以看出，使用 EnumMap 可以很方便地为枚举类型在不同的环境中绑定到不同的值上。本例子中 getURL 绑定到 URL 上，在其他的代码中可能又被绑定到数据库驱动上去。

#### EnumSet 类

EnumSet 是枚举类型的高性能 Set 实现，它要求放入它的枚举常量必须属于同一枚举类型。EnumSet 提供了许多工厂方法以便于初始化，如表 2 所示。

表 2 EnumSet 类的常用方法

| 方法名称 | 描述 |
| allOf(Class<E> element type) | 创建一个包含指定枚举类型中所有枚举成员的 EnumSet 对象 |
| complementOf(EnumSet<E> s) | 创建一个与指定 EnumSet 对象 s 相同的枚举类型 EnumSet 对象， 并包含所有 s 中未包含的枚举成员 |
| copyOf(EnumSet<E> s) | 创建一个与指定 EnumSet 对象 s 相同的枚举类型 EnumSet 对象， 并与 s 包含相同的枚举成员 |
| noneOf(<Class<E> elementType) | 创建指定枚举类型的空 EnumSet 对象 |
| of(E first,e...rest) | 创建包含指定枚举成员的 EnumSet 对象 |
| range(E from ,E to) | 创建一个 EnumSet 对象，该对象包含了 from 到 to 之间的所有枚 举成员 |

EnumSet 作为 Set 接口实现，它支持对包含的枚举常量的遍历。

```
for(Operation op:EnumSet.range(Operation.PLUS,Operation.MULTIPLY))
{
    doSomeThing(op);
}
```