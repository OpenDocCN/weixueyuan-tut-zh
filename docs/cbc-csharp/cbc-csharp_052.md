# C#装箱和拆箱（值类型和引用类型）

> 原文：[`c.biancheng.net/view/2844.html`](http://c.biancheng.net/view/2844.html)

C# ToString 方法用于将任意的数据类型转换成字符串类型，例如将整数类型转换成字符串类型。

```

int a=100;
string str=a.ToString();
```

这样整型变量 a 即可被转换成字符串类型。

在 C# 语言中数据类型分为值类型和引用类型，将值类型转换为引用类型的操作称为装箱，相应地将引用类型转换成值类型称为拆箱。

在上面的转换中 int 类型是值类型，而 string 类型是引用类型，当将值类型变量 a 的值转换成引用类型变量 str 时就是一个装箱的操作， 而拆箱操作则是将引用类型变量 str 的值再转换成整型的过程，转换的代码如下。

```

a=int.Parse(str);
```

这样就完成了一个简单的装箱和拆箱操作。

【实例】利用装箱和拆箱功能，可通过允许值类型的任何值与 Object 类型的值相互转换，将值类型与引用类型链接起来

根据题目要求，代码如下。

```

int val = 100;
object obj = val;
Console.WriteLine("对象的值 = {0}", obj);
//这是一个装箱的过程，是将值类型转换为引用类型的过程
int val = 100;
object obj = val;
int num = (int) obj;
Console.WriteLine("num: {0}", num);
//这是一个拆箱的过程，是将值类型转换为引用类型，再由引用类型转换为值类型的过程
```