# Java 替换特殊字符

在本实例中为保证学生输入的 Java 文件名合格，将利用 StringBuffe r 类实现对 Java 文件名中的特殊符号进行替换，包括将中文状态下的“。”和全角状态下的替换为英文状态下的。

在本例实现过程中主要使用了 StringBuffer 类的字符查找和替换方法，实现代码如下：

```
import java.util.Scanner;
public class Test2()
{
    public static void main(String[] args)
    {
        System.out.println("请输入你要提交的 Java 文件名称：");
        Scanner input=new Scanner(System.in);
        String fileName=input.next();    //获取用户输入的 java 文件名称

        //定义 StringBuffer 对象，字符串内容为用户输入的 java 文件名称
        StringBuffer file=new StringBuffer(fileName);

        //获取英文状态下的"."是否存在
        int index=file.lastIndexOf(".");

        //判断中文状态下的"。"和"."是否存在
        int errIndex1=file.lastIndexOf("。");
        int errIndex2=file.lastIndexOf(".");

        if(index!=-1&&file.substring(index+1,file.length()).equals("java"))
        {
            System.out.println("作业提交成功!");
        }
        else if(errIndex1!=-1&&file.substring(errIndex1+1,file.length()).equals("java"))
        {
            //将中文状态下的"。"改为英文状态下的"."
            file.setCharAt(errIndex1,'.');
            System.out.println("你的书写有误，已改正为："+file+"\r\n 提交成功！");
        }
        else if(errIndex2!=-1&&file.substring(errIndex2+1,file.length()).equals("java"))
        {
            //将全角状态下的"."改为英文状态下的"."
            file.setCharAt(errIndex2,'.');
            System.out.println("你的书写有误，已改正为："+file+"\r\n 提交成功！");
        }
        else
        {
            System.out.println("你提供的 java 文件名称有误，请核实！");
        }
    }
}
```

运行该程序，当用户录入的 Java 文件名称中的为中文状态的“。”和“.”时，修改为英文状态下的“.”，然后再进行提交，如下所示：

```
请输入你要提交的 Java 文件名称：
myexapmle。java
你的书写有误，已改正为：myexample.java
```

在该程序中，首先定义了一个 String 类型的变量，并赋值为用户输入的 Java 文件名称。 接着使用 StringBuffer 的 StringBuffer(String str) 构造函数将该变量的值作为 StringBuffer 对象的值，然后获取用户输入字符串中（英文和中文）的位置，紧接着使用多重 if 选择语句判断用户输入的字符串中是否包含英文状态下的，并且后面的字符串是否为“java”，如果条件成立，则表示用户输入的信息无误，提交成功；在 else if 中判断用户输入的字符串中是否包含中文状态下的“。”和“.”，并且“。”和后面的字符串为“java”，如果判断通过，则调用 StringBuffer 类中的 setCharAt() 方法修改“。”为“.”，再提交。