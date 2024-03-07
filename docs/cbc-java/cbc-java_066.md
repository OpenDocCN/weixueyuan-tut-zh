# Java 日期格式化（DateFormat 类和 SimpleDateFormat 类）

格式化日期表示将日期/时间格式转换为预先定义的日期/时间格式。例如将日期“Fri May 18 15:46:24 CST2016” 格式转换为 “2016-5-18 15:46:24 星期五”的格式。

在 Java 中，可以使用 DateFormat 类和 SimpleDateFormat 类来格式化日期，下面详细介绍这两个格式化日期类的使用。

## DateFormat 类

DateFormat 是日期/时间格式化子类的抽象类，它以与语言无关的方式格式化并解析日期或时间。日期/时间格式化子类（如 SimpleDateFormat）允许进行格式化（也就是日期→文本）、解析（文本→日期）和标准化日期。

在创建 DateFormat 对象时不能使用 new 关键字，而应该使用 DateFormat 类中的静态方法 getDateInstance()，示例代码如下：

```
DateFormat df=DateFormat.getDatelnstance();
```

在创建了一个 DateFormat 对象后，可以调用该对象中的方法来对日期/时间进行格式化。DateFormat 类中常用方法如表 1 所示。

表 1 DateFormat 类的常用方法

| 方法 | 描述 |
| String format(Date date) | 将 Date 格式化日期/时间字符串 |
| Calendar getCalendar() | 获取与此日期/时间格式相关联的日历 |
| static DateFormat getDateInstance() | 获取具有默认格式化风格和默认语言环境的日期格式 |
| static DateFormat getDateInstance(int style) | 获取具有指定格式化风格和默认语言环境的日期格式 |
| static DateFormat getDateInstance(int style, Locale locale) | 获取具有指定格式化风格和指定语言环境的日期格式 |
| static DateFormat getDateTimeInstance() | 获取具有默认格式化风格和默认语言环境的日期/时间 格式 |
| static DateFormat getDateTimeInstance(int dateStyle,int timeStyle) | 获取具有指定日期/时间格式化风格和默认语言环境的 日期/时间格式 |
| static DateFormat getDateTimeInstance(int dateStyle,int timeStyle,Locale locale) | 获取具有指定日期/时间格式化风格和指定语言环境的 日期/时间格式 |
| static DateFormat getTimeInstance() | 获取具有默认格式化风格和默认语言环境的时间格式 |
| static DateFormat getTimeInstance(int style) | 获取具有指定格式化风格和默认语言环境的时间格式 |
| static DateFormat getTimeInstance(int style, Locale locale) | 获取具有指定格式化风格和指定语言环境的时间格式 |
| void setCalendar(Calendar newCalendar) | 为此格式设置日历 |
| Date parse(String source) | 将给定的字符串解析成日期/时间 |

格式化样式主要通过 DateFormat 常量设置。将不同的常量传入到表 1 所示的方法中，以控制结果的长度。DateFormat 类的常量如下。

*   SHORT：完全为数字，如 12.5.10 或 5:30pm。
*   MEDIUM：较长，如 May 10，2016。
*   LONG：更长，如 May 12，2016 或 11:15:32am。
*   FULL：是完全指定，如 Tuesday、May 10、2012 AD 或 11:l5:42am CST。

使用 DateFormat 类格式化曰期/时间的示例如下：

```
//获取不同格式化风格和中国环境的日期
DateFormat df1=DateFormat.getDateInstance(DateFormat.SHORT,Locale.CHINA);
DateFormat df2=DateFormat.getDateInstance(DateFormat.FULL,Locale.CHINA);
DateFormat df3=DateFormat.getDateInstance(DateFormat.MEDIUM,Locale.CHINA);
DateFormat df4=DateFormat.getDateInstance(DateFormat.LONG,Locale.CHINA);

//获取不同格式化风格和中国环境的时间
DateFormat df5=DateFormat.getTimeInstance(DateFormat.SHORT,Locale.CHINA);
DateFormat df6=DateFormat.getTimeInstance(DateFormat.FULL,Locale.CHINA);
DateFormat df7=DateFormat.getTimeInstance(DateFormat.MEDIUM,Locale.CHINA);
DateFormat df8=DateFormat.getTimeInstance(DateFormat.LONG,Locale.CHINA);

//将不同格式化风格的日期格式化为日期字符串
String date1=df1.format(new Date());
String date2=df2.format(new Date());
String date3=df3.format(new Date());
String date4=df4.format(new Date());

//将不同格式化风格的时间格式化为时间字符串
String time1=df5.format(new Date());
String time2=df6.format(new Date());
String time3=df7.format(new Date());
String time4=df8.format(new Date());

//输出日期
System.out.println("SHORT："+date1+" "+time1);
System.out.println("FULL："+date2+" "+time2);
System.out.println("MEDIUM："+date3+" "+time3);
System.out.println("LONG："+date4+" "+time4);
```

运行该段代码，输出的结果如下：

```
SHORT：18-10-15 上午 9:30
FULL：2018 年 10 月 15 日 星期一 上午 09 时 30 分 43 秒 CST
MEDIUM：2018-10-15 9:30:43
LONG：2018 年 10 月 15 日 上午 09 时 30 分 43 秒
```

该示例主要介绍了 DateFormat 类中方法与常量的结合使用，通过使用 DateFomat 类可以对日期进行不同风格的格式化。

## SimpleDateFormat 类

如果使用 DateFormat 类格式化日期/时间并不能满足要求，那么就需要使用 DateFormat 类的子类——SimpleDateFormat。

SimpleDateFormat 是一个以与语言环境有关的方式来格式化和解析日期的具体类，它允许进行格式化（日期→文本）、解析（文本→日期）和规范化。SimpleDateFormat 使得可以选择任何用户定义的日期/时间格式的模式。

SimpleDateFormat 类主要有如下 3 种构造方法。

*   SimpleDateFormat()：用默认的格式和默认的语言环境构造 SimpleDateFormat。
*   SimpleDateFormat(String pattern)：用指定的格式和默认的语言环境构造 SimpleDateF ormat。
*   SimpleDateFormat(String pattern,Locale locale)：用指定的格式和指定的语言环境构造 SimpleDateF ormat。

SimpleDateFormat 自定义格式中常用的字母及含义如表 2 所示。

表 2 日期/时间格式中的字母及其含义与示例

| 字母 | 含义 | 示例 |
| y | 年份。一般用 yy 表示两位年份，yyyy 表示 4 位年份 | 使用 yy 表示的年扮，如 11； 使用 yyyy 表示的年份，如 2011 |
| M | 月份。一般用 MM 表示月份，如果使用 MMM，则会 根据语言环境显示不同语言的月份 | 使用 MM 表示的月份，如 05； 使用 MMM 表示月份，在 Locale.CHINA
语言环境下，如“十月”；在 Locale.US
语言环境下，如 Oct |
| d | 月份中的天数。一般用 dd 表示天数 | 使用 dd 表示的天数，如 10 |
| D | 年份中的天数。表示当天是当年的第几天， 用 D 表示 | 使用 D 表示的年份中的天数，如 295 |
| E | 星期几。用 E 表示，会根据语言环境的不同， 显示不 同语言的星期几 | 使用 E 表示星期几，在 Locale.CHINA 语 言环境下，如“星期四”；在 Locale.US 语
言环境下，如 Thu |
| H | 一天中的小时数（0~23)。一般用 HH 表示小时数 | 使用 HH 表示的小时数，如 18 |
| h | 一天中的小时数（1~12)。一般使用 hh 表 示小时数 | 使用 hh 表示的小时数，如 10 (注意 10 有 可能是 10 点，也可能是 22 点） |
| m | 分钟数。一般使用 mm 表示分钟数 | 使用 mm 表示的分钟数，如 29 |
| s | 秒数。一般使用 ss 表示秒数 | 使用 ss 表示的秒数，如 38 |
| S | 毫秒数。一般使用 SSS 表示毫秒数 | 使用 SSS 表示的毫秒数，如 156 |

#### 例 1

编写 Java 程序，使用 SimpleDateFormat 类格式化当前日期并打印，日期格式为“xxxx 年 xx 月 xx 日星期 xxx 点 xx 分 xx 秒”，具体的实现代码如下：

```
import java.text.SimpleDateFormat;
import java.util.Date;
public class Test13
{
    public static void main(String[] args)
    {
        Date now=new Date();    //创建一个 Date 对象，获取当前时间
        //指定格式化格式
        SimpleDateFormat f=new SimpleDateFormat("今天是 "+"yyyy 年 MM 月 dd 日 E HH 点 mm 分 ss 秒");
        System.out.pnntln(f.format(now));    //将当前时间袼式化为指定的格式
    }
}
```

该程序的运行结果如下：

```
今天是 2018 年 10 月 15 日 星期一 09 点 26 分 23 秒
```