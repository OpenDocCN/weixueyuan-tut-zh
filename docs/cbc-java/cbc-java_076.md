# Java Boolean 类

Boolean 类将基本类型为 boolean 的值包装在一个对象中。一个 Boolean 类的对象只包含一个类型为 boolean 的字段。此外，此类还为 boolean 和 String 的相互转换提供了很多方法，并提供了处理 boolean 时非常有用的其他一些常用方法。

## Boolean 类的构造方法

Boolean 类有以下两种构造形式：

```
Boolean(boolean boolValue);
Boolean(String boolString);
```

其中 boolValue 必须是 true 或 false（不区分大小写），boolString 包含字符串 true（不区分大小写），那么新的 Boolean 对象将包含 true；否则将包含 false。

## Boolean 类的常用方法

在 Boolean 类内部包含了一些和 Boolean 操作有关的方法，见表 1。

表 1 Boolean 类中的常用方法

| 方法 | 返回值 | 功能 |
| booleanValue() | boolean | 将 Boolean 对象的值以对应的 boolean 值返回 |
| equals(Object obj) | boolean | 判断调用该方法的对象与 obj 是否相等。当且仅当参数不是 null，且与调用该 方法的对象一样都表示同一个 boolean 值的 Boolean 对象时，才返回 true |
| parseBoolean(String s) | boolean | 将字符串参数解析为 boolean 值 |
| toString() | string | 返回表示该 boolean 值的 String 对象 |
| valueOf(String s) | boolean | 返回一个用指定的字符串表示的 boolean 值 |

#### 例 1

编写一个 Java 程序，演示如何使用不同的构造方法创建 Boolean 对象，并调用 booleanValue() 主法将创建的对象重新转换为 boolean 数据输出。代码如下：

```
public class Test05
{
    public static void main(String[] args)
    {
        Boolean b1=new Boolean(true);
        Boolean b2=new Boolean("ok");
        Boolean b3=new Boolean("true");
        System.out.println("b1 转换为 boolean 值是："+ b1);
        System.out.println("b2 转换为 boolean 值是："+ b2);
        System.out.println("b3 转换为 boolean 值是："+ b3);
    }
}
```

程序非常简单，运行后的输出结果如下：

```
b1 转换为 boolean 值是：true
b2 转换为 boolean 值是：false
b3 转换为 boolean 值是：true
```

## Boolean 类的常用常量

在 Boolean 类中包含了很多的常量，其中较为常用的常量如下。

*   TRUE：对应基值 true 的 Boolean 对象。
*   FALSE：对应基值 false 的 Boolean 对象。
*   TYPE：表示基本类型 boolean 的 Class 对象。