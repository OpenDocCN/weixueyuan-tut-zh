# JavaBean 规范

JavaBean 既不是 Applet，也不是 Application，从本质上来说，JavaBean 就是一组用于构建可重用组件的 Java 类库。与其他任何 Java 类一样，JavaBean 也是由属性和方法组成的。

JavaBean 的属性都具有 private 特性，方法具有 public 特性，方法是 JavaBean 的对外接口。

与一般 Java 类不同的是，标准的 JavaBean —般需遵循以下规范。

1\. 实现 java.io.Serializable 接口。

2\. 是一个公共类。

*   类中必须存在一个无参数的构造函数。
*   提供对应的 setXxx() 和 getXxx() 方法来存取类中的属性。

Java.IO.Serializable 接口的作用是序列化，JVM 将类实例转化为字节序列，当类实例被发送到另一台计算机后，会被重新组装，不用担心因操作系统不同而有所改变，序列化机制可以弥补网络传输中不同操作系统的差异问题。

在 JSP 中使用 JavaBean 组件时，创建的 JavaBean 不必实现 java.io.Serializable 接口，就可以运行。工具 JavaBean 不要求必须遵守该规范。

因功能不同，工具 JavaBean 中一般没有与属性对应的 setXxx() 和 getXxx() 方法。公共类是要求类在任何场合都可以访问。因为 JVM 默认时会自动生成无参数构造方法，所以可以不显式地写出来。

setXxx() 和 getXxx() 方法是用来存储和获取类中属性的，方法中的 Xxx 是属性的名称，根据 Java 语言命名规范，方法名中第二个单词的第一个字母大写，属性为布尔类型，可以使用 isXxx() 方法代替 getXxx() 方法。