# C++ auto 类型推导完全攻略

> 原文：[`c.biancheng.net/view/6984.html`](http://c.biancheng.net/view/6984.html)

在 C++11 之前的版本（C++98 和 C++ 03）中，定义变量或者声明变量之前都必须指明它的类型，比如 int、char 等；但是在一些比较灵活的语言中，比如 C#、JavaScript、PHP、Python 等，程序员在定义变量时可以不指明具体的类型，而是让编译器（或者解释器）自己去推导，这就让代码的编写更加方便。

C++11 为了顺应这种趋势也开始支持自动类型推导了！C++11 使用 **auto** 关键字来支持自动类型推导。

## auto 类型推导的语法和规则

在之前的 C++ 版本中，auto 关键字用来指明变量的存储类型，它和 static 关键字是相对的。auto 表示变量是自动存储的，这也是编译器的默认规则，所以写不写都一样，一般我们也不写，这使得 auto 关键字的存在变得非常鸡肋。

C++11 赋予 auto 关键字新的含义，使用它来做自动类型推导。也就是说，使用了 auto 关键字以后，编译器会在编译期间自动推导出变量的类型，这样我们就不用手动指明变量的数据类型了。

auto 关键字基本的使用语法如下：

auto name = value;

name 是变量的名字，value 是变量的初始值。

注意：auto 仅仅是一个占位符，在编译器期间它会被真正的类型所替代。或者说，C++ 中的变量必须是有明确类型的，只是这个类型是由编译器自己推导出来的。

auto 类型推导的简单例子：

auto n = 10;
auto f = 12.8;
auto p = &n;
auto url = "http://c.biancheng.net/cplus/";

下面我们来解释一下：

*   第 1 行中，10 是一个整数，默认是 int 类型，所以推导出变量 n 的类型是 int。
*   第 2 行中，12.8 是一个小数，默认是 double 类型，所以推导出变量 f 的类型是 double。
*   第 3 行中，&n 的结果是一个 int* 类型的指针，所以推导出变量 f 的类型是 int*。
*   第 4 行中，由双引号`""`包围起来的字符串是 const char* 类型，所以推导出变量 url 的类型是 const char*，也即一个常量指针。

我们也可以连续定义多个变量：

int n = 20;
auto *p = &n, m = 99;

先看前面的第一个子表达式，&n 的类型是 int*，编译器会根据 auto *p 推导出 auto 为 int。后面的 m 变量自然也为 int 类型，所以把 99 赋值给它也是正确的。

这里我们要注意，推导的时候不能有二义性。在本例中，编译器根据第一个子表达式已经推导出 auto 为 int 类型，那么后面的 m 也只能是 int 类型，如果写作`m=12.5`就是错误的，因为 12.5 是 double 类型，这和 int 是冲突的。

还有一个值得注意的地方是：使用 auto 类型推导的变量必须马上初始化，这个很容易理解，因为 auto 在 C++11 中只是“占位符”，并非如 int 一样的真正的类型声明。

## auto 的高级用法

auto 除了可以独立使用，还可以和某些具体类型混合使用，这样 auto 表示的就是“半个”类型，而不是完整的类型。请看下面的代码：

```

int  x = 0;
auto *p1 = &x;   //p1 为 int *，auto 推导为 int
auto  p2 = &x;   //p2 为 int*，auto 推导为 int*
auto &r1  = x;   //r1 为 int&，auto 推导为 int
auto r2 = r1;    //r2 为  int，auto 推导为 int
```

下面我们来解释一下：

*   第 2 行代码中，p1 为 int* 类型，也即 auto * 为 int *，所以 auto 被推导成了 int 类型。
*   第 3 行代码中，auto 被推导为 int* 类型，前边的例子也已经演示过了。
*   第 4 行代码中，r1 为 int & 类型，auto 被推导为 int 类型。
*   第 5 行代码是需要重点说明的，r1 本来是 int& 类型，但是 auto 却被推导为 int 类型，这表明当`=`右边的表达式是一个引用类型时，auto 会把引用抛弃，直接推导出它的原始类型。

接下来，我们再来看一下 auto 和 const 的结合：

```

int  x = 0;
const  auto n = x;  //n 为 const int ，auto 被推导为 int
auto f = n;      //f 为 const int，auto 被推导为 int（const 属性被抛弃）
const auto &r1 = x;  //r1 为 const int& 类型，auto 被推导为 int
auto &r2 = r1;  //r1 为 const int& 类型，auto 被推导为 const int 类型
```

下面我们来解释一下：

*   第 2 行代码中，n 为 const int，auto 被推导为 int。
*   第 3 行代码中，n 为 const int 类型，但是 auto 却被推导为 int 类型，这说明当`=`右边的表达式带有 const 属性时， auto 不会使用 const 属性，而是直接推导出 non-const 类型。
*   第 4 行代码中，auto 被推导为 int 类型，这个很容易理解，不再赘述。
*   第 5 行代码中，r1 是 const int & 类型，auto 也被推导为 const int 类型，这说明当 const 和引用结合时，auto 的推导将保留表达式的 const 类型。

最后我们来简单总结一下 auto 与 const 结合的用法：

*   当类型不为引用时，auto 的推导结果将不保留表达式的 const 属性；
*   当类型为引用时，auto 的推导结果将保留表达式的 const 属性。

## auto 的限制

前面介绍推导规则的时候我们说过，使用 auto 的时候必须对变量进行初始化，这是 auto 的限制之一。那么，除此以外，auto 还有哪些其它的限制呢？

1) auto 不能在函数的参数中使用。

这个应该很容易理解，我们在定义函数的时候只是对参数进行了声明，指明了参数的类型，但并没有给它赋值，只有在实际调用函数的时候才会给参数赋值；而 auto 要求必须对变量进行初始化，所以这是矛盾的。

2) auto 不能作用于类的非静态成员变量（也就是没有 static 关键字修饰的成员变量）中。

3) auto 关键字不能定义数组，比如下面的例子就是错误的：

char url[] = "http://c.biancheng.net/";
auto  str[] = url;  //arr 为数组，所以不能使用 auto

4) auto 不能作用于模板参数，请看下面的例子：

```

template <typename T>
class A{
    //TODO:
};

int  main(){
    A<int> C1;
    A<auto> C2 = C1;  //错误
    return 0;
}
```

## auto 的应用

说了那么多 auto 的推导规则和一些注意事项，那么 auto 在实际开发中到底有什么应用呢？下面我们列举两个典型的应用场景。

#### 使用 auto 定义迭代器

auto 的一个典型应用场景是用来定义 stl 的迭代器。

我们在使用 stl 容器的时候，需要使用迭代器来遍历容器里面的元素；不同容器的迭代器有不同的类型，在定义迭代器时必须指明。而迭代器的类型有时候比较复杂，书写起来很麻烦，请看下面的例子：

```

#include <vector>
using namespace std;

int main(){
    vector< vector<int> > v;
    vector< vector<int> >::iterator i = v.begin();
    return 0;
}
```

可以看出来，定义迭代器 i 的时候，类型书写比较冗长，容易出错。然而有了 auto 类型推导，我们大可不必这样，只写一个 auto 即可。

修改上面的代码，使之变得更加简洁：

```

#include <vector>
using namespace std;

int main(){
    vector< vector<int> > v;
    auto i = v.begin();  //使用 auto 代替具体的类型
    return 0;
}
```

auto 可以根据表达式 v.begin() 的类型（begin() 函数的返回值类型）来推导出变量 i 的类型。

#### auto 用于泛型编程

auto 的另一个应用就是当我们不知道变量是什么类型，或者不希望指明具体类型的时候，比如泛型编程中。我们接着看例子：

```

#include <iostream>
using namespace std;

class A{
public:
    static int get(void){
        return 100;
    }
};

class B{
public:
    static const char* get(void){
        return "http://c.biancheng.net/cplus/";
    }
};

template <typename T>
void func(void){
    auto val = T::get();
    cout << val << endl;
}

int main(void){
    func<A>();
    func<B>();

    return 0;
}
```

运行结果：

100
http://c.biancheng.net/cplus/

本例中的模板函数 func() 会调用所有类的静态函数 get()，并对它的返回值做统一处理，但是 get() 的返回值类型并不一样，而且不能自动转换。这种要求在以前的 C++ 版本中实现起来非常的麻烦，需要额外增加一个模板参数，并在调用时手动给该模板参数赋值，用以指明变量 val 的类型。

但是有了 auto 类型自动推导，编译器就根据 get() 的返回值自己推导出 val 变量的类型，就不用再增加一个模板参数了。

下面的代码演示了不使用 auto 的解决办法：

```

#include <iostream>
using namespace std;

class A{
public:
    static int get(void){
        return 100;
    }
};

class B{
public:
    static const char* get(void){
        return "http://c.biancheng.net/cplus/";
    }
};

template <typename T1, typename T2>  //额外增加一个模板参数 T2
void func(void){
    T2 val = T1::get();
    cout << val << endl;
}

int main(void){
    //调用时也要手动给模板参数赋值
    func<A, int>();
    func<B, const char*>();

    return 0;
}
```