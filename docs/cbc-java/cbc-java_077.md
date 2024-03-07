# Java Byte 类

Byte 类将基本类型为 byte 的值包装在一个对象中。一个 Byte 类的对象只包含一个类型为 byte 的字段。此外，该类还为 byte 和 String 的相互转换提供了方法，并提供了一些处理 byte 时非常有用的常量和方法。

## Byte 类的构造方法

Byte 类提供了两个构造方法来创建 Byte 对象。

#### 1\. Byte(byte value)

通过这种方法创建的 Byte 对象，可以表示指定的 byte 值。例如，下面的示例将 5 作为 byte 类型变量，然后再创建 Byte 对象。

```
byte my_byte=5;
Byte b=new Byte(my_byte);
```

#### 2\. Byte(String s)

通过这个方法创建的 Byte 对象，可表示 String 参数所指定的 byte 值。例如，下面的示例将 5 作为 String 类型变量，然后再创建 Byte 对象。

```
String my_byte="5";
Byte b=new Byte(my_byte);
```

注意：必须使用数值型的 String 变量作为参数才能创建成功，否则会抛出 NumberFormatException 异常。

## Byte 类的常用方法

在 Byte 类内部包含了一些和 Byte 操作有关的方法，见表 1。

表 1 Byte 类中的常用方法

| 方法 | 返回值 | 功能 |
| byteValue() | byte | 以一个 byte 值返回 Byte 对象 |
| compareTo(Byte bytel) | int | 在数字上比较两个 Byte 对象 |
| doubleValue() | double | 以一个 double 值返回此 Byte 的值 |
| intValue() | int | 以一个 int 值返回此 Byte 的值 |
| parseByte(String s) | byte | 将 String 型参数解析成等价的 byte 形式 |
| toStringO | String | 返回表示此 byte 值的 String 对象 |
| valueOf(String s) | Byte | 返回一个保持指定 String 所给出的值的 Byte 对象 |
| equals(Object obj) | boolean | 将此对象与指定对象比较，如果调用该方法的对象与 obj 相等 则返回 true，否则返回 false |

## Byte 类的常用常量

在 Byte 类中包含了很多的常量，其中较为常用的常量如下。

*   MIN_VALUE：byte 类可取的最小值。
*   MAX_VALUE：byte 类可取的最大值。
*   SIZE：用于以二进制补码形式表示的 byte 值的位数。
*   TYPE：表示基本类 byte 的 Class 实例。