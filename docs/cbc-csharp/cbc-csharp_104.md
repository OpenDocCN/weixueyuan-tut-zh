# C# StreamWriter 类：写入文件

> 原文：[`c.biancheng.net/view/2926.html`](http://c.biancheng.net/view/2926.html)

在 C# 语言中与上一节《C# StreamReader 》中介绍的 StreamReader 类对应的是 StreamWriter 类，StreamWriter 类主要用于向流中写入数据。

StreamWriter 类的构造方法也有很多，这里只列出一些常用的构造方法，如下表所示。

| 构造方法 | 说明 |
| StreamWriter(Stream stream)  | 为指定的流创建 StreamWriter 类的实例 |
| StreamWriter(string path) | 为指定路径的文件创建 StreamWriter 类的实例 |
| StreamWriter(Stream stream, Encoding encoding) | 用指定的字符编码为指定的流初始化 StreamWriter 类的一个新实例 |
| StreamWriter(string path, Encoding encoding) | 用指定的字符编码为指定的文件名初始化 StreamWriter 类的一个新实例 |

在创建了 StreamWriter 类的实例后即可调用其类成员，完成向文件中写入信息的操作。

StreamWriter 类中常用的属性和方法如下表所示。

| 属性或方法 | 作用 |
| bool AutoFlush | 属性，获取或设置是否自动刷新缓冲区 |
| Encoding Encoding | 只读属性，获取当前流中的编码方式 |
| void Close() | 关闭流 |
| void Flush() | 刷新缓冲区 |
| void Write(char value) | 将字符写入流中 |
| void WriteLine(char value) | 将字符换行写入流中 |
| Task WriteAsync(char value) | 将字符异步写入流中 |
| Task WriteLineAsync(char value)  | 将字符异步换行写入流中 |

在上表中给出的方法中，Write、WriteAsync、WriteLineAsync 方法还有很多不同类型写入的重载方法，这里没有一一列出。

下面通过实例演示 StreamWriter 类的应用。

【实例】向 D 盘 code 文件夹的 test.txt 文件中写入姓名和手机号码。

根据题目要求，代码如下。

```

class Program
{
    static void Main(string[] args)
    {
        string path = @"D:\\code\\test.txt";
        //创建 StreamWriter 类的实例
        StreamWriter streamWriter = new StreamWriter(path);
        //向文件中写入姓名
        streamWriter.WriteLine("小张");
        //向文件中写入手机号
        streamWriter.WriteLine("13112345678");
        //刷新缓存
        streamWriter.Flush();
        //关闭流
        streamWriter.Close();
    }
}
```

执行上面的代码，即可将姓名和手机号码写入到名为 test.txt 的文件中。