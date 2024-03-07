# Java 校验文件名和邮箱地址

假设，在作业提交系统中学生需要录入提交的 Java 文件名称及要提交到的邮箱地址，那么就需要对学生输入的这些信息进行校验，判断输入是否有误。校验的规则为：录入的文件名称必须以“.java”结尾，录入的邮箱地址中必须包含有“@”符号和“.”符号，且“@”在“.”之前。

在这里使用 String 类中的 indexOf() 方法、charAt() 方法和 lastIndexOf() 方法来完成此程序。具体实现代码如下：

```
import java.util.Scanner;
public class Testl7
{
    public static void main(String[] args)
    {
        boolean filecon=false;    //判断文件名是否合法
        boolean emailcon=false;    //判断邮箱地址是否合法
        System.out.println("************ 欢迎进入作业提交系统 ************");
        Scanner input=new Scanner(System.in);
        System.out.println("请输入要提交的 Java 文件名称：");
        String name=input.next();    //获取输入的 Java 文件名
        System.out.println("请输入要提交到的邮箱地址：");
        String email=input.next();    //获取输入的邮箱地址
        //检查输入的文件名是否合法
        int index=name.lastIndexOf('.');    // 获取"n"所在的位置
        //判断合法
        if(index!=-1&&name.charAt(index+1)=='j'
                    &&name.charAt(index+2)=='a'
                    &&name.charAt(index+3)=='v'
                    &&name.charAt(index+4)=='a')
        {
            filecon=true;
        }
        else
        {
            System.out.println("输入的文件名无效！");
        }
        //检查邮箱地址是否合法
        if(email.indexOf('@')!=1&&email.indexOf('.')>email.indexOf('@'))
        {
            emailcon=true;
        }
        else
        {
            System.out.println("输入的邮箱地址无效!");
        }
        //输出校验的结果
        if(filecon&&emailcon)
        {
            System.out.println("作业提交成功!");
        }
        else
        {
            System.out.println("作业提交失败!");
        }
    }
}
```

运行该程序，当用户输入的文件名是以“.java”结尾，并且输入的邮箱地址中包含有“@”符号和符号，符号在符号之后，才能检测通过，打印“作业提交成功！”， 如下所示：

```
************ 欢迎进入作业提交系统 ************
请输入要提交的 Java 文件名称：
answer.java
请输入要提交到的邮箱地址：
answer@qq.com
作业提交成功!
```

否则，打印“作业提交失败！”，如下所示：

```
************ 欢迎进入作业提交系统 ************
请输入要提交的 Java 文件名称：
abcjava
请输入要提交到的邮箱地址：
abcjava@qqcom
输入的文件名无效！
输入的邮箱地址无效!
作业提交失败!
```

在该程序中，首先使用 lastlndexOf() 方法获取用户输入 Java 文件中的所在的位置。因为 Java 文件是以“.java”结尾的，因此，可以使用 charAt() 方法来检测“.”之后的字符串是否是 java，即通过如下所示的条件判断表达式即可判断出用户输入的 Java 文件名是否以“.java”结尾。

```
index!=-1&&name.charAt(index+1)='j'
         &&name.charAt(index+2)='a'
         &&name.charAt(index+3)='v'
         &&name.charAt(index+4)='a'
```

在检测邮箱地址是否合法时，首先使用了 indexOf() 方法判断用户输入的邮箱地址中是否含有符号，然后判断邮箱地址中的符号是否在“@”符 号之后，如果这两个条件都满足，则邮箱地址是合法的。

当文件名称和邮箱地址的检测都通过时，则打印“作业提交成功！”，否则打印“作业提交失败！”。