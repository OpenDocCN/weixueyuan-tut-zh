# Java 转换大小写（toLowerCase()和 toUpperCase()）

String 类的 toLowerCase() 方法可以将字符串中的所有字符全部转换成小写，而非字母的字符不受影响。语法格式如下：

```
字符串名.toLowerCase()    //将字符串中的字母全部转换为小写，非字母不受影响
```

toUpperCase() 则将字符串中的所有字符全部转换成大写，而非字母的字符不受影响。语法格式如下：

```
字符串名.toUpperCase()    //将字符串中的字母全部转换为大写，非字母不受影响
```

例如：

```
String str="abcdef 我 ghijklmn";
System.out.println(str.toLowerCase());    //输出：abcdef 我 ghijklmn
System.out.println(str.toUpperCase());    //输出：ABCDEF 我 GHIJKLMN
```

#### 例 1

下面的实例代码演示了如何对字符串应用大写和小写转换。

```
public static void main(String[] args)
{
    String en="The Day You Went Away";    //定义原始字符串
    System.out.println("原始字符串："+en);
    System.out.println("使用 toLowerCase() 方法之后为："+en.toLowerCase());
    System.out.println("使用 toUpperCase() 方法之后为："+en.toUpperCase());

    en="sun sun 是太阳，圆圆挂在 SKY 上";    //定义原始字符串
    System.out.println("原始字符串："+en);
    System.out.println("使用 toLowerCase() 方法之后为："+en.toLowerCase());
    System.out.printIn("使用 toUpperCase() 方法之后为："+en.toUpperCase());

    en="select id,name,sex,address,birthday from students";
    System.out.println("原始字符串："+en);    //定义原始字符串
    System.out.println("使用 toLowerCase() 方法之后为："+en.toLowerCase());
    System.out.println("使用 toUpperCase() 方法之后为："+en.toUpperCase());
}
```

代码比较简单，运行后的输出结果如下：

```
原始字符串：The Day You Went Away
使用 toLowerCase() 方法之后为：the day you went away
使用 toUpperCase() 方法之后为：THE DAY YOU WENT AWAY
原始字符串：sun sun 是太阳，圆圆挂在 SKY 上
使用 toLowerCase() 方法之后为：sun sun 是太阳，圆圆挂在 sky 上
使用 toUpperCase() 方法之后为：SUN SUN 是太阳，圆圆挂在 SKY 上
原始字符串：select id,name,sex,address,birthday from students
使用 toLowerCase() 方法之后为：select id,name,sex,address,birthday from students
使用 toUpperCase() 方法之后为：SELECT ID,NAME,SEX,ADDRESS,BIRTHDAY FROM STUDENTS
```