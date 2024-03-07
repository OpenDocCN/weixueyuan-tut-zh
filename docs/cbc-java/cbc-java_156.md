# Java 什么是反射机制？反射机制的概念

Java 反射机制是 Java 语言的一个重要特性，它在服务器程序和中间件程序中得到了广泛运用。在服务器端，往往需要根据客户的请求，动态调用某一个对象的特定方法。此外，在 ORM 中间件的实现中，运用 java 反射机制可以读取任意一个 JavaBean 的所有属性，或者给这些属性赋值。

Java 反射机制主要提供了以下功能，这些功能都位于 `java.lang.reflect` 包。

*   在运行时判断任意一个对象所属的类。
*   在运行时构造任意一个类的对象。
*   在运行时判断任意一个类所具有的成员变量和方法。
*   在运行时调用任意一个对象的方法。
*   生成动态代理。

众所周知，所有 Java 类均继承了 Object 类，在 Object 类中定义了一个 getClass() 方法，该方法返回同一个类型为 Class 的对象。例如，下面的示例代码：

```
Class labelCls=label1.getClass();    //label1 为 JLabel 类的对象
```

利用 Class 类的对象 labelCls 可以访问 labelCls 对象的描述信息、JLabel 类的信息以及基类 Object 的信息。表 1 列出了通过反射可以访问的信息。

表 1 反射可访问的常用信息

| 类型 | 访问方法 | 返回值类型 | 说明 |
| 包路径 | getPackage() | Package 对象 | 获取该类的存放路径 |
| 类名称 | getName() | String 对象 | 获取该类的名称 |
| 继承类 | getSuperclass() | Class 对象 | 获取该类继承的类 |
| 实现接口 | getlnterfaces() | Class 型数组 | 获取该类实现的所有接口 |
| 构造方法 | getConstructors() | Constructor 型数组 | 获取所有权限为 public 的构造方法 |
| getDeclaredContxuectors() | Constructor 对象 | 获取当前对象的所有构造方法 |
| 方法 | getMethods() | Methods 型数组 | 获取所有权限为 public 的方法 |
| getDeclaredMethods() | Methods 对象 | 获取当前对象的所有方法 |
| 成员变量 | getFields() | Field 型数组 | 获取所有权限为 public 的成员变量 |
| getDeclareFileds() | Field 对象 | 获取当前对象的所有成员变量 |
| 内部类 | getClasses() | Class 型数组 | 获取所有权限为 public 的内部类 |
| getDeclaredClasses() | Class 型数组 | 获取所有内部类 |
| 内部类的声明类 | getDeclaringClass() | Class 对象 | 如果该类为内部类，则返回它的成员类，否则返回 null |

如表 1 所示，在调用 getFields() 和 getMethods() 方法时将会依次获取权限为 public 的字段和变量，然后将包含从超类中继承到的成员实量和方法。而通过 getDeclareFields() 和 getDeclareMethod()只是获取在本类中定义的成员变量和方法。