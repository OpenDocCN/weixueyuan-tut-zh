# C#方法的定义

> 原文：[`c.biancheng.net/view/2808.html`](http://c.biancheng.net/view/2808.html)

在我们学习 C# 语言的过程中会发现之前的应用程序中默认会生成一个主方法 Main()，它是执行程序的入口和出口。

方法是将完成同一功能的内容放到一起，方便书写和调用的一种方式，也体现了面向对象语言中封装的特性。

定义方法的语法形式如下。

访问修饰符    修饰符    返回值类型    方法名(参数列表)
{
    语句块;
}

其中：

#### 1) 访问修饰符

所有类成员访问修饰符都可以使用，如果省略访问修饰符，默认是 private。

#### 2) 修饰符

在定义方法时修饰符包括 virtual（虚拟的）、abstract（抽象的）、override（重写的）、static（静态的）、sealed（密封的）。override 是在类之间继承时使用的。

#### 3) 返回值类型

用于在调用方法后得到返回结果，返回值可以是任意的数据类型，如果指定了返回值类型，必须使用 return 关键字返回一个与之类型匹配的值。如果没有指定返回值类型，必须使用 void 关键字表示没有返回值。

#### 4) 方法名

对方法所实现功能的描述。方法名的命名是以 Pascal 命名法为规范的。

#### 5)参数列表

在方法中允许有 0 到多个参数，如果没有指定参数也要保留参数列表的小括号。参数的定义形式是“数据类型参数名”，如果使用多个参数，多个参数之间需要用逗号隔开。

【实例 1】在 Test 类中定义一个方法输出指定字段的值。

根据题目要求，代码如下。

```

namespace code_1
{
    class Test
    {
        private int id;                         //定义私有的整型字段 id
        public readonly string name;            //定义公有的只读字符串类型字段 name
        internal static int age;                //定义内部的静态的整型字段 age
        private const string major = "计算机";  //定义私有的字符串类型常量 major

        private void PrintMsg()
        {
            Console.WriteLine("编号：" + id);
            Console.WriteLine("姓名：" + name);
            Console.WriteLine("年龄：" + age);
            Console.WriteLine("专业：" + major);
        }
    }
}
```

执行上面的代码并不会有任何输出效果，因为在 C# 语言中方法必须调用才能执行其中的代码。

【实例 2】创建 Compute 类，分别定义 4 个方法实现加法、减法、乘法、除法的操作。

根据题目要求，代码如下。

```

namespace code_1
{
    class Compute
    {
        //加法
        private double Add(double num1,double num2)
        {
            return num1 + num2;
        }
        //减法
        private double Minus(double num1,double num2)
        {
            return num1 - num2;
        }
        //乘法
        private double Multiply(double num1,double num2)
        {
            return num1 * num2;
        }
        //除法
        private double Divide(double num1,double num2)
        {
            return num1 / num2;
        }
    }
}
```

从上面的代码可以看出，在 Compute 类中定义了 4 个方法，每个方法都是有参数、有返回值的。

因为方法的定义有多种形式，所以可以用很多方法完成上面的实例。有兴趣的小伙伴可以尝试定义不同形式的方法来实现上面实例的功能。