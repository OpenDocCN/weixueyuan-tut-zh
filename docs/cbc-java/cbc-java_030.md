# Java 根据出生日期计算（判断）星座

下面我们来了解一下十二星座对应的日期划分范围。

```
白羊：0321~0420          天秤：0924~1023
金牛：0421~0521          天蝎：1024~1122
双子：0522~0621          射手：1123~1221
巨蟹：0622~0722          摩羯：1222~0120
狮子：0723~0823          水瓶：0121~0219
处女：0824~0923          双鱼：0220~0320
```

例如，出生日期为 0609（6 月 9 号），则对应的是双子座。

根据上述描述，在程序中需要用户输入一个 4 位数字，再根据这个数字所处的范围进行判断，其中前两位是月份，后两位是日期。在这里使用 switch 语句判断出生的月份，然后根据日期确定星座名称。

实现代码如下：

```
public static void main(String[] args)
{
    System.out.println("请输入您的出生年月（如 0123 表示 1 月 23 日）：");
    Scanner sc=new Scanner(System.in);
    int monthday=sc.nextInt();
    int month=monthday/100;
    int day=monthday%100;
    String xingzuo="";
    switch (month)
    {
　　    case 1:
            xingzuo=day<21?"摩羯座":"水瓶座";
            break;
　　    case 2:
            xingzuo=day<20? "水瓶座":"双鱼座";
            break;
　　    case 3:
            xingzuo=day<21?"双鱼座":"白羊座";
            break;
　　    case 4:
            xingzuo=day<21?"白羊座":"金牛座";
            break;
        case 5:
            xingzuo=day<22?"金牛座":"双子座";
            break;
　　    case 6:
            xingzuo=day<22?"双子座":"巨蟹座";
            break;
　　    case 7:
            xingzuo=day<23?"巨蟹座":"狮子座";
            break;
　　    case 8:
            xingzuo=day<24?"狮子座":"处女座";
            break;
　　    case 9:
            xingzuo=day<24?"处女座":"天秤座";
            break;
　　    case 10:
            xingzuo=day<24?"天秤座":"天蝎座";
            break;
　　    case 11:
            xingzuo=day<23?"天蝎座":"射手座";
            break;
　　    case 12:
            xingzuo=day<22?"射手座":"摩羯座";
            break;
    }
    System.out.println("您的星座是：" +xingzuo);
}
```

上述代码中， 首先声明变量 monthday 保存用户输入的信息，然后用 month 和 day 变量分别表示月份和日期。接下来 switch 语句根据月份判断执行 case 子句中的代码，在每个 case 子句中，使用三元运算符根据日期判断所属的星座。

最终运行效果如下所示：

```
请输入您的出生年月（如 0123 表示 1 月 23 日）：
0521
您的星座是：金牛座
```

```
请输入您的出生年月（如 0123 表示 1 月 23 日）：
1109
您的星座是：天蝎座
```