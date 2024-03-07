# Java 批量替换字符串

假设有一段文本里面有很多错误，如错别字。现在使用 Java 中的字符串替换方法对它进行批量修改和纠正，其中用到了 String 类的 replace() 方法、replaceFirst() 方法和 replaceAll() 方法。

创建一个 Java 类，然后在主方法 main() 中编写代码，具体代码如下所示。

```
public static void main(String[] args)
{
    //定义原始字符串
    String intro="今天时星其天，外面时下雨天。妈米去买菜了，漏网在家写作业。"+"语文作业时”其”写 5 行，数学使第 10 页。";

    //将文本中的所有"时"和"使"都替换为"是"
    String newStrfirst=intro.replaceAll("[时使]","是");

    //将文本中的所有"妈米"改为"妈妈"
    String newStrSecond=newStrFirst.replaceAll("妈米","妈妈");

    //将文本中的所有"漏网"改为"留我"
    String newStrThird=newStrSecond.replaceAll("漏网","留我");

    //将文本中第一次出现的"其"改为"期"
    String newStrFourth=newStrThird.repiaceFirst("[其]","期");

    //输出最终字符串
    System.out.println(newStrFourth);
}
```

运行该程序，输出的正确字符串内容如下：

```
今天是星期天，外面是下雨天。妈妈去买菜了，留我在家写作业。语文作业是”其”写 5 行，数学是第 10 页。
```