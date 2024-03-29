# Java 程序的基本要素

尽管《Java 程序应用实例》一节中的实例很简单，但它包括所有 Java 程序具有的基本要素及几个关键特性。现在以《Java 程序应用实例》中的 HelloJava.java 为例说明 Java 程序的基本要素及编码规范。在该应用程序中包含的元素有：标识符、关键字、注释、修饰符、语句、块、类以及 main() 方法。要创建一个程序，必须理解这些基本元素。下面分别介绍这些元素。

## 标识符

Java 中标识符是为方法、变量或其他用户定义项所定义的名称。标识符可以有一个或多个字符。在 Java 语言中，标识符的构成规则如下。

*   标识符由数字（0~9）和字母（A~Z 和 a~z）、美元符号（$）、下划线（_）以及 Unicode 字符集中符号大于 0xC0 的所有符号组合构成（各符号之间没有空格）。
*   标识符的第一个符号为字母、下划线和美元符号，后面可以是任何字母、数字、美元符号或下划线。

另外，Java 区分大小写，因此 myvar 和 MyVar 是两个不同的标识符。

提示：标识符命名时，切记不能以数字开头，也不能使用任何 Java 关键字作为标识符，而且不能赋予标识符任何标准的方法名。

标识符分为两类，分别为关键字和用户自定义标识符。

1.  关键字是有特殊含义的标识符，如 true、false 表示逻辑的真假。
2.  用户自定义标识符是由用户按标识符构成规则生成的非保留字的标识符，如 abc 就是一个标识符。

提示：使用标识符时一定要注意，或者使用关键字，或者使用自定义的非关键字标识符。此外，标识符可以包含关键字，但不能与关键字重名。

例如以下合法与不合法标识符。

*   合法标识符：date、$2011、_date、D_$date 等。
*   不合法的标识符：123.com、2com、for、if、fruit 等。

标识符用来命名常量、变量、类和类的对象等。因此，一个良好的编程习惯要求命名标识符时，应赋予它一个有意义或有用途的名字。

## 关键字

关键字（或者保留字）是对编译器有特殊意义的固定单词，不能在程序中做其他目的使用。关键字具有专门的意义和用途，和自定义的标识符不同，不能当作一般的标识符来使用。例如，在 HelloJava.java 中的 class 就是一个关键字，它用来声明一个类，其类名称为 HelloJava。public 也是关键字，它用来表示公共类。另外，static 和 void 也是关键字，它们的使用将在本教程后面的章节中详细介绍。

Java 的关键字对 Java 编译器有特殊的意义，它们用来表示一种数据类型，或者表示程序的结构等。保留字是为 Java 预留的关键字，它们虽然现在没有作为关键字，但在以后的升级版本中有可能作为关键字。

Java 语言目前定义了 51 个关键字，这些关键字不能作为变量名、类名和方法名来使用。以下对这些关键字进行了分类。

1.  数据类型：boolean、int、long、short、byte、float、double、char、class、interface。
2.  流程控制：if、else、do、while、for、switch、case、default、break、continue、return、try、catch、finally。
3.  修饰符：public、protected、private、final、void、static、strict、abstract、transient、synchronized、volatile、native。
4.  动作：package、import、throw、throws、extends、implements、this、supper、instanceof、new。
5.  保留字：true、false、null、goto、const。

提示：由于 Java 区分大小写，因此 public 是关键字，而 Public 则不是关键字。但是为了程序的清晰及可读性，要尽量避免使用关键字的其他形式来命名。

## 注释

注释是对程序语言的说明，有助于开发者和用户之间的交流，方便理解程序。注释不是编程语句，因此被编译器忽略。

在 Java 中，一行注释以双斜杠（//）标识；多行注释包含在“/*”和“*/”之间；文档注释包含在“/**”和“*/”之间。例如给 JavaApplet.java 文件添加注释。

```
import java.applet.*;    //导入 java.applet 包下的所有类
import java.awt.*;    //导入 java.awt 包下的所有类
public class JavaApplet extends Applet
{
    /**
    *Applet 初始化方法
    *@see java.applet.Applet 类
    *@return 无
    */
    public void init()
    {
        setSize(300,200);    //指定大小
    }
    /**
    *Applet 画图操作方法
    *@see java.applet.Applet
    */
    public void paint(Graphics g)
    {
        g.drawString("你好 Java 世界！",100,30);    //绘制图像
    }
}
```

在 Java 中，一行注释以双斜杠（//）标识；多行注释包含在“/*”和“*/”之间；文档注释包含在“/**”和“*/”之间。例如给 JavaApplet.java 文件添加注释。当编译器执行到“//”时，就会忽略该行“//”之后的所有文本；当执行到“/*”时，会扫描下一个“*/”并忽略“/*”和“*/”之间的任何文本；当执行到“/**”时，也会扫描下一个“*/”并忽略“/**”和“*/”之间的任何文本内容。

## 修饰符

Java 使用了一定数量的称为修饰符的关键字，这些修饰符指定了数据、方法和类的属性以及它们的使用方法。例如 public 和 static 都是修饰符，另外还有 private、final、abstract 和 protected。 一个 public 数据、方法或者类可以被其他的类访问，而一个 private 数据或方法不能被其他类访问。后面的章节中将会详细讲解这些修饰符的用法。

## 语句和块

语句代表一个动作或一系列动作。例如 JavaApplet.java 文件的 init() 方法中，只有一条语句“SetSize(300，200);”，该语句指定了画布的大小。

在 Java 语言中语句是以分号（;）来结尾的。 程序中的大括号形成了组织程序中的语句块。在 Java 中，每个块以大括号“{”开始，以大括号“}”结束。每个类都有组织该类数据和方法的类块，每个方法也都有组织该方法内语句的方法块。块可以嵌套，也就是说一个块可以放置在另一个块内，如下面的代码所示。

```
public class Hello
{
    public static void main(String[] args)
    {
        System.out.println("你好，Java 世界！");
    }
}
```

该段代码中的 main() 方法块放置在了 Hello 类的块中。

## 类

类是 Java 的基本架构，在 Java 中编程必须理解类并学会编写和使用类。上面已经讲到过，在 Java 中，每个 Java 程序至少必须有一个类。每个类都要先声明，然后定义该类的数据和方法。

Java 中可以定义多个类，每个 Java 程序的定义都是使用一个或多个类完成的。在上述例子中，Hello 即为类名。有关类的知识将在本教程的后面章节中做详细讲解。

## 方法

在 Java 中，方法同 C 语言中的函数作用相似，都可以执行一系列动作。如上例中 `System.out.println("你好，Java 世界！")`，是执行向控制台输出字符串的信息。其中 System.out 是标准输出对象，println 是该对象的一个方法，它由执行向标准输出设备显示信息的一系列操作语句集组成，通过调用带有字符串参数的语句对它进行操作。

在上例子中，"你好，Java 世界！"即为参数。可以用不同的参数调用同一个 println() 方法，显示不同的信息。

## main()方法

main() 方法是 Java 程序中非常重要的方法，它是程序的入口。如上例子中，在 Hello 类中定义了一个 main() 方法，其中 public 表示访问权限，指明所有的类都可以使用这个方法；static 指明该方法是一个类方法（又称为静态方法），它可以通过类名直接调用；void 则指明 main() 方法不返回任何值。

对一个应用程序来说，main() 方法是必需的，而且必须按照上面的格式来定义。Java 解释器在没有生成任何实例的情况下，以 main() 方法作为入口来执行程序。Java 程序中可以定义多个类，每个类中可以定义多个方法，但 main() 方法作为程序的入口，只能有一个。

main() 方法定义中，小括号“()”中的“String[] args”是传递给 main() 方法的参数，参数名为 args，它是类 String 的一个实例。参数可以为 0 个或多个，多个参数间用括号分隔。main() 方法的实现由大括号“{}”括起来。