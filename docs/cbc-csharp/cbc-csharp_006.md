# 第一个 C#程序

> 原文：[`c.biancheng.net/view/2779.html`](http://c.biancheng.net/view/2779.html)

了解控制台应用程序通常是认识 C# 应用程序的第一步，它是一个在类似于 DOS 的界面中输入与输出的程序，是学习 C# 程序的基本语法最方便的程序。

接下来我们介绍一下如何创建控制台应用程序。

创建控制台应用程序非常简单，依次选择“文件”一“新建”一“项目”命令，弹出如下图所示的对话框。

![新建项目对话框](img/86b77ebdd9cff3f042e4197ec55f491e.png)
在其中选择“控制台应用程序”选项，并为该项目设置名称、位置以及解决方案名称，单击“确定”按钮即可创建控制台应用程序，效果如下图所示。

> 需要注意解决方案名称不一定与项目名称相同，在同一个解决方案中允许设置多个项目。

![第一个控制台程序](img/0a71e1e36f5e2a410193f4ce1410df30.png)
从上图所示的界面中可以看出在解决方案资源管理器中创建了一个名为 Solution 的解决方案，并在该解决方案中创建了一个名为 Test 的控制台应用程序。

在 Test 应用程序中包含了一个名为 Program.cs 的类文件，该文件中的代码如下图所示。

在该代码中的第 11 行到第 13 行是 Main 方法，Main 方法是一个特殊的方法，并且在每个类中只能有一个，只需要将代码写到 Main 方法中，在项目运行后 Main 方法中的代码就会执行。

在控制台应用 程序的控制台界面中输出内容的方法如下。

```

Console.Write();  //向控制台界面不换行输出内容
Console.WriteLine(); //向控制台界面换行输出内容
```

下面使用控制台应用程序向控制台输出“第一个 C# 程序”和“这是一个控制台应用程序”，实现的代码如下。

```

namespace test
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("第一个 C# 程序");
            Console.WriteLine("这是第一个控制台程序");
        }
    }
}
```

按 Ctrl+F5 组合键运行程序，效果如下图所示。

![第一个控制台程序运行效果](img/3f85ea3ac02dccd717ffe027f23480f2.png)
关于使用 `Console.Write()` 方法来完成输出信息的操作请自行尝试并查看运行的效果。