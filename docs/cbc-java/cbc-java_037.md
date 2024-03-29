# Java continue 语句详解

continue 语句是跳过循环体中剩余的语句而强制执行下一次循环，其作用为结束本次循环，即跳过循环体中下面尚未执行的语句，接着进行下一次是否执行循环的判定。

continue 语句类似于 break 语句，但它只能出现在循环体中。它与 break 语句的区别在于：continue 并不是中断循环语句，而是中止当前迭代的循环，进入下一次的迭代。简单来讲，continue 是忽略循环语句的当次循环。

注意：continue 语句只能用在 while 语句、for 语句或者 foreach 语句的循环体之中，在这之外的任何地方使用它都会引起语法错误。

#### 例 1

循环录入 Java 课程的学生成绩，统计分数大于 80 分（包括等于）的学生人数。这时，需要定义变量 count 记录大于 80 分（包括等于）的学生人数，每循环一次，需要判断录入的学生分数是否大于等于 80，如果是，则执行 count+1，否则执行 continue，跳过本次循环，继续下次循环。代码如下：

```
public class Test26
{
    public static void main(String[] args)
    {
        int score=0;    //记录课程的分数
        int count=0;    //记录成绩大于等于 80 分的人数
        Scanner input=new Scanner(System.in);
        for(int i=0;i<10;i++)
        {
            System.out.println("请输入第"+(i+1)+"位学生的 Java 成绩：");
            score=input.nextInt();    //获取用户录入的学生成绩
            if(score<80)    //判断用户录入的学生成绩是否小于 80
            {    
                continue;    //如果小于 80，跳过本次循环，继续下次循环
            }
            count++;    //如果用户录入的分数大于等于 80，则人数加 1
        }
        System.out.println("Java 成绩在 80 分以上的学生人数为："+count);
    }
}
```

在该程序中，变量 count 表示 80 分以上的学生人数。for 循环从 0 开始循环，循环 10 次，可以理解为班里只有 10 个学生，需要录入 10 个学生的成绩。

每循环一次都需要录入一次学生的成绩，同时需要判断用户录入的学生成绩是否小于 80 分，如果小于 80 分，则跳出本次循环，即“count++”不执行，大于 80 分的人数不累加，然后执行下一次循环。只有当“score<80”的条件表达式不成立时，才执行“count++”。

运行结果如下所示：

```
请输入第 1 位学生的 Java 成绩：
80
请输入第 2 位学生的 Java 成绩：
20
请输入第 3 位学生的 Java 成绩：
40
请输入第 4 位学生的 Java 成绩：
90
请输入第 5 位学生的 Java 成绩：
78
请输入第 6 位学生的 Java 成绩：
74
请输入第 7 位学生的 Java 成绩：
48
请输入第 8 位学生的 Java 成绩：
78
请输入第 9 位学生的 Java 成绩：
58
请输入第 10 位学生的 Java 成绩：
45
Java 成绩在 80 分以上的学生人数为：2
```