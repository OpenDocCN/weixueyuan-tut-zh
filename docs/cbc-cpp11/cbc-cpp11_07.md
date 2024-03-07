# C++11 对模板实例化中连续右尖括号>>的改进

> 原文：[`c.biancheng.net/view/3729.html`](http://c.biancheng.net/view/3729.html)

在 C++98/03 的泛型编程中，模板实例化有一个很烦琐的地方，那就是连续两个右尖括号（>>）会被编译器解释成右移操作符，而不是模板参数表的结束。

【实例】C++98/03 中不支持连续两个右尖括号的示例。

```

template <typename T>
struct Foo
{
      typedef T type;
};

template <typename T>
class A
{
    // ...
};

int main(void)
{
    Foo<A<int>>::type xx;  //编译出错
    return 0;
}
```

使用 gcc 编译时，会得到如下错误提示：

error: '>>' should be '>>' within a nested template argument list Foo<A<int>>::type xx;

意思就是，`Foo<A<int>>`这种写法是不被支持的，要写成这样`Foo<A<int> >`（注意两个右尖括号之间的空格）。

这种限制无疑是很没有必要的。在 C++ 的各种成对括号中，目前只有右尖括号连续写两个会出现这种二义性。static_cast、reinterpret_cast 等 C++ 标准转换运算符，都是使用`<>`来获得待转换类型（type-id）的。若这个 type-id 本身是一个模板，用起来会很不方便。

现在在 C++11 中，这种限制终于被取消了。在 C++11 标准中，要求编译器对模板的右尖括号做单独处理，使编译器能够正确判断出`>>`是一个右移操作符还是模板参数表的结束标记（delimiter，界定符）。

不过这种自动化的处理在某些时候会与老标准不兼容，比如下面这个例子：

```

template <int N>
struct Foo
{
    // ...
};

int main(void)
{
    Foo<100 >> 2> xx;
    return 0;
}
```

在 C++98/03 的编译器中编译是没问题的，但 C++11 的编译器会显示：

error: expected unqualif?ied-id before '>' token Foo<100 >> 2> xx;

解决的方法是这样写：

Foo<(100 >> 2)> xx;  // 注意括号

这种加括号的写法其实也是一个良好的编程习惯，使得在书写时倾向于写出无二义性的代码。

#### 扩展阅读

各种 C++98/03 编译器除了支持标准（ISO/IEC 14882：2003 及其之前的标准）之外，还自行做了不少的拓展。这些拓展中的一部分，后来经过了 C++ 委员会的斟酌和完善，进入了 C++11。

所以有一部分 C++11 的新特征，在一些 C++98/03 的老编译器下也是可以支持的，只是由于没有标准化，无法保证各种平台/编译器下的兼容性。比如像 Microsoft Visual C++2005 这种不支持 C++11 的编译器，在对模板右尖括号的处理上和现在的 C++11 是一致的。