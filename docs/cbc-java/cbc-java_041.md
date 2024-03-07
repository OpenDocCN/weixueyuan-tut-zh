# Java 定义字符串

在 Java 中定义一个字符串最简单的方法是用双引号把它包围起来。这种用双引号括起来的一串字符实际上都是 String 对象，如字符串"Hello"在编译后即成为 String 对象。因此也可以通过创建 String 类的实例来定义字符串。

不论使用哪种形式创建字符串，字符串对象一旦被创建，其值是不能改变的，但可以使用其他变量重新赋值的方式进行更改。

## 直接定义字符串

直接定义字符串是指使用双引号表示字符串中的内容，例如"Hello Java"、"Java 编程"等。具体方法是用字符串常量直接初始化一个 String 对象，示例如下：

```
String str="Hello Java";
```

或者

```
String str;
str="Heilo Java";
```

注意：字符串变量必须经过初始化才能使用。

#### 例 1

下面的实例演示了直接创建字符串的几种用法。

```
String str="我是一只小小鸟";    //结果：我是一只小小鸟
String word;
word="I am a bird";    //结果：I am a bird
word="<h1>to fly</h1>";    //结果：<h1>to fly</h1>
word="Let\'s say that it\'s true";    // 结果：Let's say that it's true
System.out.println(word);
word="北京\\上海\\广州";    //结果：北京\上海\广州
```

## 使用 String 类定义

在 Java 中每个双引号定义的字符串都是一个 String 类的对象。因此，可以通过使用 String 类的构造方法来创建字符串，该类位于 java.lang 包中，作用域是 final。

String 类的构造方法有多种重载形式，每种形式都可以定义字符串。下面介绍最常用的几种形式。

#### 1\. String()

初始化一个新创建的 String 对象，表示一个空字符序列。

#### 2\. String(String original)

初始化一个新创建的 String 对象，使其表示一个与参数相同的字符序列。换句话说，新创建的字符串是该参数字符串的副本。例如：

```
String str1=new String("Hello Java");
String str2=new String(str1);
```

这里 str1 和 str2 的值是相等的。

#### 3\. String(char[ ]value)

分配一个新的字符串，将参数中的字符数组元素全部变为字符串。该字符数组的内容已被复制，后续对字符数组的修改不会影响新创建的字符串。例如：

```
char a[]={'H','e','l','l','0'};
String sChar=new String(a);
a[1]='s';
```

上述 sChar 变量的值是字符串"Hello"。 即使在创建字符串之后，对 a 数组中的第 2 个元素进行了修改，但未影响 sChar 的值。

#### 4\. String(char[] value,int offset,int count)

分配一个新的 String，它包含来自该字符数组参数一个子数组的字符。offset 参数是子数组第一个字符的索引，count 参数指定子数组的长度。该子数组的内容已被赋值，后续对字符数组的修改不会影响新创建的字符串。例如：

```
char a[]={'H','e','l','l','o'};
String sChar=new String(a,1,4);
a[1]='s';
```

上述 sChar 变量的值是字符串"Hello"。该构造方法使用字符数组中的部分连续元素来创建字符串对象。offset 参数指定起始索引值，count 指定截取元素的个数。创建字符串对象后，即使在后面修改了 a 数组中第 2 个元素的值，对 sChar 的值也没有任何影响。