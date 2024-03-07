# Java 日期查询

本实例使用有关日期处理和日期格式化的类实现一个日期查询的功能，即查询指定日期所在周的周一日期、两个指定日期间相差的天数和指定日期为所在周的星期几的日期 3 个功能。

从功能上来看，本实例至少需要定义 3 个方法，分别完成：获取指定日期所在周的周一日期、获取两个指定日期的相差天数以及获取指定日期为所在周的星期几的日期这 3 个功能，同时需要定义 Date 类型的日期与 String 类型之间的转换方法。

(1) 新建 Exercise 类，在该类中导入需要的包。代码如下：

```
import java.text.Parse Exception;
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;
public class Exercise
{
    //在该类中有 5 个方法，在下面的步骤中将会讲解，这里省略
}
```

(2) 在 Exercise 类中定义获取指定日期所在周的周一日期的方法——getMonday()，该方法是一个静态方法，返回值为 String 类型，并且需要传递一个 Date 类型的参数，然后将该日期参数作为 Calendar 对象的日期，并调用 Calendar 类的 set(int field,int value)方法改变 Calendar 对象中的日期，再将使用 SimpleDateFormat 类格式化后的日期返回。

getMonday() 方法的代码实现如下：

```
//获取指定日期所在周的周一日期
public static String getMonday(Date date)
{
    Calendar c=Calendar.getInstance();
    c.setTime(date);
    c.set(Calendar.DAY_OF_WEEK,Calendar.MONDAY);
    return new SimpleDateFormat("yyyy-MM-dd").format(c.getTime());
}

```

(3) 继续在 Exercise 类中定义获取两个日期间相差天数的方法——getTwoDay()，该方法需要传递两个 String 类型的参数。

在该方法的主体中，首先需要创建一个 SimpleDateFormat 类对象，并指定日期的格式，然后需要调用 SimpleDateFormat 对象的 parse() 方法，将传递过来的两个 String 类型的日期转换为 Date 类型，并将这两个 Date 类型的日期进行运算，得出相差的天数，返回一个 int 类型的变量。

getTwoDay() 方法的代码实现如下：

```
//获取两个日期间相差的天数
public static int getTwoDay(String sj1, String sj2)
{
    SimpleDateFormat myFormatter=new SimpleDateFormat("yyyy-MM-dd");
    int day=0;
    try
    {
        Date date=myFormatter.parse(sj1);
        Date mydate=myFormatter.parse(sj2);
        //计算两个日期间相差的天数
        day=(int)((date.getTime()-mydate.getTime())/(24*60*60*1000));
    }
    catch(Exception e)
    {
        return 0;
    }
    return day;
}

```

(4) 在 Exercise 类中继续创建两个静态方法，分别完成 String 类型的日期与 Date 类型日期的互换功能。代码如下：

```
//将 String 类型的日期转换为 Date 类型
public static Date strToDate(String strDate)
{
    SimpleDateFormat sdf=new SimpleDateFormat("yyyy-MM-dd");
    Date date=null;
    try
    {
        date=sdf.parse(strDate);
    }
    catch(ParseException e)
    {
        e.printStackTrace();
    }
    return date;
}
//将 Date 类型的日期转换为 String 类型
public static String DateToStr(Date date)
{
    SimpleDateFormat sdf=new SimpleDateFormat("yyyy-MM-dd");
    String strDate=sdf.format(date);
    return strDate;
}
```

(5) 在 Exercise 类中定义获取指定日期所在周的指定星期的日期方法——getWeek()，该方法需要传递两个 String 类型的参数，其中第一个参数表示指定的日期，第二个参数表示星期几。

在该方法的主体中，首先调用了 strToDate() 方法，将 String 类型的日期转换为 Date 类型的日期，并将该日期作为 Calendar 类对象日期，然后判断传递过来的第二个参数的值，使用 Calendar 类中的 set(int field，int value) 方法改变日期，从而将格式化后的日期返回。

getWeek()方法的代码实现如下：

```
//获得一个日期所在周的星期几的日期，如要找出 2011 年 5 月 1 日所在周的星期一是几号
//@param sdate 所确定的日期形式 2011-05-01
//@param num 要确定的是周几（1 表示周一，2 表示周二）
//@return
public static String getWeek(String sdate, String num)
{
    //再转换为时间
    Date dd=strToDate(sdate);
    Calendar c=Calendar.getInstance();
    c.setTime(dd);
    int weekNo=Integer.parseInt(num);
    switch (weekNo)
    {
        case 1:
            c.set(Calendar.DAY_OF_WEEK,Calendar.MONDAY);    //返回星期一所在的日期
            break;
        case 2:
            c.set(Calendar.DAY_OF_WEEK,Calendar.TUESDAY);    //返回星期二所在的日期
            break;
        case 3:
            c.set(Calendar.DAY_OF_WEEK,Calendar.WEDNESDAY);    //返回星期三所在的日期
            break;
        case 4:
            c.set(Calendar.DAY_OF_WEEK,Calendar.THURSDAY);    //返回星期四所在的日期
            break;
        case 5:
            c.set(Calendar.DAY_OF_WEEK,Calendar.FRIDAY);    //返回星期五所在的日期
            break;
        case 6:
            c.set(Calendar.DAY_OF_WEEK,Calendar.SATURDAY);    //返回星期六所在的曰期
            break;
        case 7:
            c.set(Calendar.DAY_OF_WEEK,Calendar.SUNDAY);    //返回星期曰所在的日期
            break;
    }
    return new SimpleDateFormat("yyyy-MM-dd").format(c.getTime());
}
```

(6) 创建测试类 Test15，调用 Exercise 类的方法，完成查询当前日期所在周的周一日期，并获取本周的周一曰期与当前曰期相差的天数，同时还可以查询本周中任意星期几的曰期。代码实现如下：

```
package com.common.mxl;
import java.util.Date;
import java.util.Scanner;
import com.common.mxl.Exercise;
public class Test15
{
    public static void main(String[] args)
    {
        Scanner input=new Scanner(System.in);
        Date date=new Date();
        String monday=Exercise.getMonday(date);
        System.out.print("这周一的日期为【"+monday+"】，");
        String nowStr=Exercise.DateToStr(date);       //将 Date 类型的日期转换为 String 类型
        int num=Exercise.getTwoDay(nowStr,monday);
        System.out.print("与今天相差【"+num+"】天");
        SyStem.out.println("\n 请输入您要查询本周星期几的日期（1 表示星期一，2 表示星期二…)：");
        String weekNo=input.next();       //获取用户输入的星期
        int no=Integer.parseInt(weekNo);
        //将用户输入的星期转换为 int 类型，以便使用 switch 语句
        String strWeekNo="";
        switch(no)
        {
            case 1:
                strWeekNo="星期一";
                break;
            case 2:
                strWeekNo="星期二";
                break;
            case 3:
                strWeekNo="星期三";
                break;
            case 4:
                strWeekNo="星期四";
                break;
            case 5:
                strWeekNo="星期五";
                break;
            case 6:
                strWeekNo="星期六";
                break;
            case 7:
                strWeekNo="星期日";
                break;
        }
        System.out.println("本周【"+strWeekNo+"】的日期为："+Exercise.getWeek(nowStr,weekNo));
    }
}
```

在本程序中，综合使用了 Date 类、Calendar 类和 SimpleDateFormat 类完成了时间处理功能。在源码中，多次使用 Calendar 类的 set(int field,int value) 方法来改变日期，从而获取用户查询的日期。该方法在日期操作方面比较常用，大家需要熟练掌握该方法。

该程序的运行结果如下所示：

```
这周一的日期为【2018-10-15】，与今天相差【0】天
请输入您要查询本周星期几的日期（1 表示星期一，2 表示星期二…)：
1
本周【星期一】的日期为：2018-10-15
```