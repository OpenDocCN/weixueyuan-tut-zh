# Java break 语句详解

在 Java 中，break 语句有 3 种作用，分别是：在 switch 语句中终止一个语句序列、使用 break 语句直接强行退出循环和使用 break 语句实现 goto 的功能。

## 在 switch 语句中终止一个语句序列

在 switch 语句中终止一个语句序列，就是在每个 case 子句块的最后添加语句“break;”，

## 使用 break 语句直接强行退出循环

可以使用 break 语句强行退出循环，忽略循环体中的任何其他语句和循环的条件判断。在循环中遇到 break 语句时，循环被终止，在循环后面的语句重新开始。

#### 例 1

小明参加了一个 1000 米的长跑比赛，在 100 米的跑道上，他循环地跑着，每跑一圈，剩余路程就会减少 100 米，要跑的圈数就是循环的次数。但是，在每跑完一圈时，教练会问他是否要坚持下去，如果回答 y，则继续跑，否则表示放弃。

使用 break 语句直接强行退出循环的示例如下：

```
import java.util.Scanner;
public class Test24
{
    public static void main(String[] args)
    {
        Scanner input=new Scanner(System.in);    //定义变量存储小明的回答
        String answer="";    //一圈 100 米，1000 米为 10 圈，即为循环的次数
        for(int i=0;i<10;i++)
        {
            System.out.println("跑的是第"+(i+1)+"圈");
            System.out.println("还能坚持吗？");    //获取小明的回答
            answer=input.next();    //判断小明的回答是否为 y？如果不是，则放弃，跳出循环
            if(!answer.equals("y"))
            {
                System.out.println("放弃");
                break;
            }
            // 循环之后的代码
            System.out.println("加油！继续！");
        }
    }
}
```

该程序运行后的效果如下所示：

```
跑的是第 1 圈
还能坚持吗？
y
加油！继续！
跑的是第 2 圈
还能坚持吗？
y
加油！继续！
跑的是第 3 圈
还能坚持吗？
n
放弃
```

尽管 for 循环被设计为从 0 执行到 10，但是当小明的回答不是 y 时，break 语句终止了程序的循环，继续执行循环体外的代码，输出“加油！继续！”。

break 语句能用于任何 Java 循环中，包括人们有意设置的无限循环。在一系列嵌套循环中使用 break 语句时，它将仅仅终止最里面的循环。例如：

```
public class BreakDemo1
{
     public static void main(String[] args)
     {
        //外循环，循环 5 次
        for (int i=0;i<5;i++)
        {
            System.out.print("第"+(i+1)+"次循环：");

            //内循环，设计为循环 10 次
            for(int j=0;j<10;j++)
            {
                //判断 j 是否等于 3，如果是，则终止循环
                if(j==3)
                {
                    break;
                }
                System.out.print("内循环的第"+(j+1)+"次循环\t");
            }
            System.out.println();
        }
    }
}
```

该程序运行结果如下所示：

```
第 1 次循环：内循环的第 1 次循环 内循环的第 2 次循环 内循环的第 3 次循环
第 2 次循环：内循环的第 1 次循环 内循环的第 2 次循环 内循环的第 3 次循环
第 3 次循环：内循环的第 1 次循环 内循环的第 2 次循环 内循环的第 3 次循环
第 4 次循环：内循环的第 1 次循环 内循环的第 2 次循环 内循环的第 3 次循环
第 5 次循环：内循环的第 1 次循环 内循环的第 2 次循环 内循环的第 3 次循环
```

从程序运行结果来看，在内部循环中的 break 语句仅仅终止了所在的内部循环，外部循环没有受到任何的影响。

注意：一个循环中可以有一个以上的 break 语句，但是过多的 break 语句会破坏代码结构。switch 循环语句中的 break 仅影响 switch 语句，不会影响循环。

#### 例 2

编写一个 Java 程序，允许用户输入 6 门课程成绩，如果录入的成绩为负则跳出循环；如果录入 6 门合法成绩，则计算已有成绩之和。

使用 break 语句的实现代码如下：

```
public class Test25
{
    public static void main(String[] args)
    {
        int score; //每门课的成绩
        int sum=0; //成绩之和
        boolean con=true; //记录录入的成绩是否合法
        Scanner input=new Scanner(System.in);
        System.out.println("请输入学生的姓名：");
        String name=input.next(); //获取用户输入的姓名
        for(int i=1;i<=6;i++)
        {
            System.out.println("请输入第"+i+"门课程的成绩：");
            score=input.nextInt();//获取用户输入的成绩
            if(score<0)
            { //判断用户输入的成绩是否为负数，如果为负数，终止循环
                con=false;
                break;
            }
            sum=sum+score; //累加求和
        }
        if(con)
        {
            System.out.println(name+"的总成绩为："+sum);
        }
        else
        {
            System.out.println("抱歉，分数录入错误，请重新录入！");
        }
    }
}
```

运行程序，当用户录入的分数低于 0 时，则输出“抱歉，分数录入错误，请重新录入！”信息，否则打印学生的总成绩。输出结果如下所示。

```
请输入学生的姓名：
zhangpu
请输入第 1 门课程的成绩：
100
请输入第 2 门课程的成绩：
75
请输入第 3 门课程的成绩：
-8
抱歉，分数录入错误，请重新录入！
```

```
请输入学生的姓名：
zhangpu
请输入第 1 门课程的成绩：
100
请输入第 2 门课程的成绩：
68
请输入第 3 门课程的成绩：
73
请输入第 4 门课程的成绩：
47
请输入第 5 门课程的成绩：
99
请输入第 6 门课程的成绩：
84
zhangpu 的总成绩为：471
```

在该程序中，当录入第 3 门课的成绩时，录入的成绩为负数，判断条件“score<0”为 true，执行“con=false”，用 con 来标记录入是否有误。接着执行 break 语句，执行完之后程序并没有继续执行条件语句后面的语句，而是直接退出 for 循环。之后执行下面的条件判断语句，判断 boolean 变量的 con 是否为 true，如果为 true，则打印总成绩；否则打印“抱歉，分数录入错误，请重新录入！”。

## 使用 break 语句实现 goto 的功能

break 语句可以实现 goto 的功能，并且 Java 定义了 break 语句的一种扩展形式来处理退出嵌套很深的循环这个问题。

通过使用扩展的 break 语句，可以终止执行一个或者几个任意代码块，这些代码块不必是一个循环或一个 switch 语句的一部分。同时这种扩展的 break 语句带有标签，可以明确指定从何处重新开始执行。

break 除了具有 goto 退出深层循环嵌套作用外，还保留了一些程序结构化的特性。

标签 break 语句的通用格式如下：

```
break label;
```

label 是标识代码块的标签。当执行这种形式的 break 语句时，控制权被传递出指定的代码块。被加标签的代码块必须包围 break 语句，但是它不需要直接包围 break 的块。也就是说，可以使用一个加标签的 break 语句来退出一系列的嵌套快，但是不能使用 break 语句将控制权传递到不包含 break 语句的代码块。

用标签（label）可以指定一个代码块，标签可以是任何合法有效的 Java 标识符，后跟一个冒号。加上标签的代码块可以作为 break 语句的对象，使程序在加标签的块的结尾继续执行。

下面是使用带标签的 break 语句的示例。

```
public class GotoDemo
{
    public static void main(String[] args)
    {
        label:for(int i=0;i<10;i++)
        {
            for(int j=0;j<8;j++)
            {
                System.out.println(j);
                if(j%2!=0)
                {
                    break label;
                }
            }
        }
    }
}
```

以上程序的执行结果为：

```
0
1
```

这里的 label 是标签的名称，可以为 Java 语言中任意合法的标识符。标签语句必须和循环匹配使用，使用时书写在对应的循环语句的上面，标签语句以冒号结束。如果需要中断标签语句对应的循环，可以采用 break 后面跟标签名的方式。

如在上面代码中，当 j 为 1 时，“j%2!=0”条件表达式成立，则 label 标签所代表的最外层循环终止。