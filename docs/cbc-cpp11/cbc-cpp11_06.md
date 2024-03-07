# C++返回值类型后置（跟踪返回值类型）

> 原文：[`c.biancheng.net/view/3727.html`](http://c.biancheng.net/view/3727.html)

在泛型编程中，可能需要通过参数的运算来得到返回值的类型。考虑下面这个场景：

```

template <typename R, typename T, typename U>
R add(T t, U u)
{
    return t+u;
}
int a = 1; float b = 2.0;
auto c = add<decltype(a + b)>(a, b);
```

我们并不关心 a+b 的类型是什么，因此，只需要通过 decltype(a+b) 直接得到返回值类型即可。但是像上面这样使用十分不方便，因为外部其实并不知道参数之间应该如何运算，只有 add 函数才知道返回值应当如何推导。

那么，在 add 函数的定义上能不能直接通过 decltype 拿到返回值呢？

```

template <typename T, typename U>
decltype(t + u) add(T t, U u)  // error: t、u 尚未定义
{
    return t + u;
}
```

当然，直接像上面这样写是编译不过的。因为 t、u 在参数列表中，而 C++ 的返回值是前置语法，在返回值定义的时候参数变量还不存在。

可行的写法如下：

```

template <typename T, typename U>
decltype(T() + U()) add(T t, U u)
{
    return t + u;
}
```

考虑到 T、U 可能是没有无参构造函数的类，正确的写法应该是这样：

```

template <typename T, typename U>
decltype((*(T*)0) + (*(U*)0)) add(T t, U u)
{
    return t + u;
}
```

虽然成功地使用 decltype 完成了返回值的推导，但写法过于晦涩，会大大增加 decltype 在返回值类型推导上的使用难度并降低代码的可读性。

因此，在 C++11 中增加了**返回类型后置（trailing-return-type，又称跟踪返回类型）**语法，将 decltype 和 auto 结合起来完成返回值类型的推导。

返回类型后置语法是通过 auto 和 decltype 结合起来使用的。上面的 add 函数，使用新的语法可以写成：

```

template <typename T, typename U>
auto add(T t, U u) -> decltype(t + u)
{
    return t + u;
}
```

为了进一步说明这个语法，再看另一个例子：

```

int& foo(int& i);
float foo(float& f);

template <typename T>
auto func(T& val) -> decltype(foo(val))
{
    return foo(val);
}
```

如果说前一个例子中的 add 使用 C++98/03 的返回值写法还勉强可以完成，那么这个例子对于 C++ 而言就是不可能完成的任务了。

在这个例子中，使用 decltype 结合返回值后置语法很容易推导出了 foo(val) 可能出现的返回值类型，并将其用到了 func 上。

返回值类型后置语法，是为了解决函数返回值类型依赖于参数而导致难以确定返回值类型的问题。有了这种语法以后，对返回值类型的推导就可以用清晰的方式（直接通过参数做运算）描述出来，而不需要像 C++98/03 那样使用晦涩难懂的写法。