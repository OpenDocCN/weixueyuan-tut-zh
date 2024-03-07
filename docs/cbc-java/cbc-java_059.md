# Java 正则表达式验证 IP 地址

IP 地址是网络上每台计算机的唯一标识，因此 IP 地址的错误输入将使程序无法运行。下面使用前面学习的正则表达式知识，编写一个 Java 程序来验证 IP 地址是否合法。

在定义正则表达式之前，首先要知道一个正确 IP 地址的格式及组成部分。IP 地址由 4 字节构成，每字节的值为 0~255，各字节通过一个句点分隔。因此，IP 地址中的每字节有至少一个，至多三个数字。

下面是为 IP 地址编写的正则表达式：

```
\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}
```

以上正则表达式对 IP 做简单的匹配，不完全和 IP 的规则一致。如果想完全匹配，读者可以重新修改匹配的正则表达式。

创建 Test22 类，在该类的 main() 方法中 编写验证 IP 地址的代码。代码如下：

```
import java.util.Scanner;
import java.util.regex.Matcher;
import java.util.regex.Pattern;
public class Test22
{
    public static void main(String[] args)
    {
        String regex="\\d{1,3}\\.\\d{1,3}\\.\\d{1,3}\\.\\d{1,3}";
        String answer="Y";
        do
        {
            System.out.print("请输入 IP 地址：");
            Scanner scan=new Scanner(System.in);
            String phone=scan.next();    //接收用户在控制台输入的电话号码
            Pattern pattern=Pattern.compile(regex);    //编译正则表达式
            Matcher matcher=pattern.matcher(phone);    //创建给定输入模式的匹配器
            boolean bool=matcher.matches();
            if(bool)
            {   //如果验证通过
                System.out.println("输入的 IP 地址正确。");
            }
            else
            {
                System.out.println("输入的 IP 地址格式错误。");
            }
            System.out.print("是否继续输入？（Y/N 或者 y/n)");
            answer=scan.next();
        }while(answer.equalsIgnoreCase("Y"));
        System.out.println("程序结束。");
    }
}
```

运行程序，然后输入不同格式的 IP 地址,输出结果如下所示：

```
请输入 IP 地址：11.154.15.1
输入的 IP 地址正确。
是否继续输入？（Y/N 或者 y/n)y
请输入 IP 地址：22a.7.68.9
输入的 IP 地址格式错误。
是否继续输入？（Y/N 或者 y/n)n
程序结束。
```