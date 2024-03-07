# C# Directory 类：文件夹操作

> 原文：[`c.biancheng.net/view/2916.html`](http://c.biancheng.net/view/2916.html)

C# 语言中 Directory 类与上一节《C# Directoryinfo》中介绍的 Directoryinfo 类的方法比较相似，这里不再详细列出其属性和方法的定义。

Directory 类是一个静态类， 不能创建该类的实例，直接通过“类名 . 类成员”的形式调用其属性和方法。

Directory 类省去了创建类实例的步骤，其他操作也与 Directoryinfo 类似。

下面通过实例来演示 Directory 类的应用。

【实例】使用 Directory 类在 D 盘上操作 code 文件夹，要求先判断是否存在该文件夹，如果存在则删除，否则创建该文件夹。

根据题目要求，代码如下。

```

class Program
{
    static void Main(string[] args)
    {
        bool flag = Directory.Exists("D:\\code");
        if (flag)
        {
            Directory.Delete("D:\\code", true);
        }
        else
        {
            Directory.CreateDirectory("D:\\code");
        }
    }
}
```

执行上面的代码，即可完成文件夹 code 的删除或创建操作。