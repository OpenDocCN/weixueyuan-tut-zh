# C#多播委托

> 原文：[`c.biancheng.net/view/2936.html`](http://c.biancheng.net/view/2936.html)

在 C# 语言中多播委托是指在一个委托中注册多个方法，在注册方法时可以在委托中使用加号运算符或者减号运算符来实现添加或撤销方法。

在现实生活中，多播委托的实例是随处可见的，例如某点餐的应用程序，既可以预定普通的餐饮也可以预定蛋糕、鲜花、水果等商品。

在这里委托相当于点餐平台，每一个类型的商品可以理解为在委托上注册的一个方法。

下面通过实例来演示多播委托的应用。

【实例】模拟点餐平台预定不同类型的商品。

根据题目要求，在实例中分别预定快餐、蛋糕、鲜花三类商品，代码如下。

```

class Program
{
    //定义购买商品委托
    public delegate void OrderDelegate();
    static void Main(string[] args)
    {
        //实例化委托
        OrderDelegate orderDelegate = new OrderDelegate(Order.BuyFood);
        //向委托中注册方法
        orderDelegate += Order.BuyCake;
        orderDelegate += Order.BuyFlower;
        //调用委托
        orderDelegate();
    }
}
class Order
{
    public static void BuyFood()
    {
        Console.WriteLine("购买快餐！");
    }
    public static void BuyCake()
    {
        Console.WriteLine("购买蛋糕！");
    }
    public static void BuyFlower()
    {
        Console.WriteLine("购买鲜花！");
    }
}
```

执行上面的代码，效果如下图所示。

![多播委托的应用](img/779196ac9db1685fe3d4d073611be29b.png)
如果已经购买了鲜花，在未调用委托时也可以撤销，在委托注册方法时使用 `-=` 操作符即可。

撤销购买鲜花操作的代码如下。

```

orderDelegate -= Order.BuyFlower;
```

如果添加了上述代码，则执行效果中就取消了购买鲜花的操作。

在使用多播委托时需要注意，在委托中注册的方法参数列表必须与委托定义的参数列表相同，否则不能将方法添加到委托上。