# Java 判断闰年平年并输出某月的天数

所谓闰年，就是指 2 月有 29 天的那一年。闰年同时满足以下条件：

*   年份能被 4 整除。
*   年份若是 100 的整数倍，须被 400 整除，否则是平年。

例如，1900 年能被 4 整除，但是因为其是 100 的整数倍，却不能被 400 整除，所以是平年；而 2000 年就是闰年；1904 年和 2004 年、2008 年等直接能被 4 整除且不能被 100 整除，都是闰年；2014 是平年。

下面综合本章学习的知识来编写一个判断闰年的案例，其主要功能如下：

1.  判断用户输入的年份是不是闰年。
2.  根据年份和月份输出某年某月的天数。

实现步骤分为以下几步：

(1) 新建一个类并在该类中导入需要的 java.util.Scanner 类，同时需要创建该类的入口方法 main()，其实现代码如下：

```
import java.util.Scanner;
public class Test27
{
    public static void main(String[] args)
    {
        // 在这里编写其他代码
    }
}
```

(2) 在 main() 方法中编写 Java 代码，获取用户输入的年份和月份，其实现代码如下：

```
Scanner sc=new Scanner(System.in);
System.out.println("请输入年份(注: 必须大于 1990 年):");
int year=sc.nextInt();
System.out.println("请输入月份:");
int month=sc.nextInt();
```

(3) 根据用户输入的年份，判断该年份是闰年还是平年，其实现代码如下：

```
boolean isRen;
if((year%4==0&&year%100!=0)||(year%400==0))
{
    System.out.println(year+"闰年");
    isRen=true;
}
else
{
    System.out.println(year+"平年");
    isRen=false;
}
```

(4) 根据用户输入的月份，判断该月的天数，其实现代码如下：

```
int day=0;
switch(month)
{
    case 1:
    case 3:
    case 5:
    case 7:
    case 8:
    case 10:
    case 12:
        day=31;
        break;
    case 4:
    case 6:
    case 9:
    case 11:
        day=30;
        break;
    default:
        if(isRen)
        {
            day=29;
        }
        else
        {
            day=28;
        }
        break;
}
System.out.println(year+"年"+month+"月共有"+day+"天");
```

(5) 该程序代码执行结果如下所示：

```
请输入年份(注: 必须大于 1990 年):
2014
请输入月份:
2
2014 平年
2014 年 2 月共有 28 天
```

```
请输入年份(注: 必须大于 1990 年):
2000
请输入月份:
2
2000 闰年
2000 年 2 月共有 29 天
```