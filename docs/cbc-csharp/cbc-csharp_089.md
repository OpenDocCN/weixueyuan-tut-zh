# C#泛型简介

> 原文：[`c.biancheng.net/view/2902.html`](http://c.biancheng.net/view/2902.html)

在前面已经介绍了类和方法的定义，那么泛型究竟有什么作用呢？在前面《C#集合》一节中介绍了集合，集合中的项允许是 object 型的值，因此可以存放任意类型的值。

例如，在 ArrayList 中以 double 类型存入学生考试成绩，但存入值时并没有做验证，存入了其他数据类型的值，代码如下。

```

ArrayList arrayList=new ArrayList();
arrayList.Add(100);
arrayList.Add("abc");
arrayList.Add(85.5);
```

在输出集合中的元素时，如果使用 double 类型来遍历集合中的元素，代码如下。

```

foreach (int d in arrayList)
{
    Console.WriteLine(d);
}
```

执行上面的代码，由于在集合中存放的并不全是 double 类型的值，因此会出现 `System.InvalidCastException` 异常，即指定的转换无效。

为了避免类似的情况产生，将集合中元素的类型都指定为 double 类型，不能在集合中输入其他类型的值，这种设置方式即为泛型的一种应用。