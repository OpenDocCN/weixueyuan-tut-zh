# C++11 lambda 匿名函数用法详解

> 原文：[`c.biancheng.net/view/7818.html`](http://c.biancheng.net/view/7818.html)

lambda 源自希腊字母表中第 11 位的 λ，在计算机科学领域，它则是被用来表示一种匿名函数。所谓匿名函数，简单地理解就是没有名称的函数，又常被称为 lambda 函数或者 lambda 表达式。

继 Python、Java、C#、PHP 等众多高级编程语言都支持 lambda 匿名函数后，C++11 标准终于引入了 lambda，本节将带领大家系统地学习 lambda 表达式的具体用法。

## lambda 匿名函数的定义

定义一个 lambda 匿名函数很简单，可以套用如下的语法格式：

[外部变量访问方式说明符] (参数) mutable noexcept/throw() -> 返回值类型
{
   函数体;
};

其中各部分的含义分别为：

1) [外部变量方位方式说明符]
[ ] 方括号用于向编译器表明当前是一个 lambda 表达式，其不能被省略。在方括号内部，可以注明当前 lambda 函数的函数体中可以使用哪些“外部变量”。

> 所谓外部变量，指的是和当前 lambda 表达式位于同一作用域内的所有局部变量。

2) (参数)
和普通函数的定义一样，lambda 匿名函数也可以接收外部传递的多个参数。和普通函数不同的是，如果不需要传递参数，可以连同 () 小括号一起省略；

3) mutable
此关键字可以省略，如果使用则之前的 () 小括号将不能省略（参数个数可以为 0）。默认情况下，对于以值传递方式引入的外部变量，不允许在 lambda 表达式内部修改它们的值（可以理解为这部分变量都是 const 常量）。而如果想修改它们，就必须使用 mutable 关键字。

注意，对于以值传递方式引入的外部变量，lambda 表达式修改的是拷贝的那一份，并不会修改真正的外部变量；

4) noexcept/throw()
可以省略，如果使用，在之前的 () 小括号将不能省略（参数个数可以为 0）。默认情况下，lambda 函数的函数体中可以抛出任何类型的异常。而标注 noexcept 关键字，则表示函数体内不会抛出任何异常；使用 throw() 可以指定 lambda 函数内部可以抛出的异常类型。

值得一提的是，如果 lambda 函数标有 noexcept 而函数体内抛出了异常，又或者使用 throw() 限定了异常类型而函数体内抛出了非指定类型的异常，这些异常无法使用 try-catch 捕获，会导致程序执行失败（本节后续会给出实例）。

5) -> 返回值类型
指明 lambda 匿名函数的返回值类型。值得一提的是，如果 lambda 函数体内只有一个 return 语句，或者该函数返回 void，则编译器可以自行推断出返回值类型，此情况下可以直接省略`-> 返回值类型`。

6) 函数体
和普通函数一样，lambda 匿名函数包含的内部代码都放置在函数体中。该函数体内除了可以使用指定传递进来的参数之外，还可以使用指定的外部变量以及全局范围内的所有全局变量。

需要注意的是，外部变量会受到以值传递还是以引用传递方式引入的影响，而全局变量则不会。换句话说，在 lambda 表达式内可以使用任意一个全局变量，必要时还可以直接修改它们的值。

> 其中，红色标识的参数是定义 lambda 表达式时必须写的，而绿色标识的参数可以省略。

比如，如下就定义了一个最简单的 lambda 匿名函数：

[]{}

显然，此 lambda 匿名函数未引入任何外部变量（[] 内为空），也没有传递任何参数，没有指定 mutable、noexcept 等关键字，没有返回值和函数体。所以，这是一个没有任何功能的 lambda 匿名函数。

#### lambda 匿名函数中的[外部变量]

对于 lambda 匿名函数的使用，令多数初学者感到困惑的就是 [外部变量] 的使用。其实很简单，无非表 1 所示的这几种编写格式。

表 1 [外部变量]的定义方式

| 外部变量格式 | 功能 |
| [] | 空方括号表示当前 lambda 匿名函数中不导入任何外部变量。 |
| [=] | 只有一个 = 等号，表示以值传递的方式导入所有外部变量； |
| [&] | 只有一个 & 符号，表示以引用传递的方式导入所有外部变量； |
| [val1,val2,...] | 表示以值传递的方式导入 val1、val2 等指定的外部变量，同时多个变量之间没有先后次序； |
| [&val1,&val2,...] | 表示以引用传递的方式导入 val1、val2 等指定的外部变量，多个变量之间没有前后次序； |
| [val,&val2,...] | 以上 2 种方式还可以混合使用，变量之间没有前后次序。 |
| [=,&val1,...] | 表示除 val1 以引用传递的方式导入外，其它外部变量都以值传递的方式导入。 |
| [this] | 表示以值传递的方式导入当前的 this 指针。 |

> 注意，单个外部变量不允许以相同的传递方式导入多次。例如 [=，val1] 中，val1 先后被以值传递的方式导入了 2 次，这是非法的。

【例 1】lambda 匿名函数的定义和使用。

```

#include <iostream>
#include <algorithm>
using namespace std;

int main()
{
    int num[4] = {4, 2, 3, 1};
    //对 a 数组中的元素进行排序
    sort(num, num+4, = -> bool{ return x < y; } );
    for(int n : num){
        cout << n << " ";
    }
    return 0;
}
```

程序执行结果为：

1 2 3 4

程序第 9 行通过调用 sort() 函数实现了对 num 数组中元素的升序排序，其中就用到了 lambda 匿名函数。而如果使用普通函数，需以如下代码实现：

```

#include <iostream>
#include <algorithm>
using namespace std;
//自定义的升序排序规则
bool sort_up(int x,int y){
return  x < y;
}

int main()
{
    int num[4] = {4, 2, 3, 1};
    //对 a 数组中的元素进行排序
    sort(num, num+4, sort_up);
    for(int n : num){
        cout << n << " ";
    }
    return 0;
}
```

此程序中 sort_up() 函数的功能和上一个程序中的 lambda 匿名函数完全相同。显然在类似的场景中，使用 lambda 匿名函数更有优势。

除此之外，虽然 lambda 匿名函数没有函数名称，但我们仍可以为其手动设置一个名称，比如：

```

#include <iostream>
using namespace std;

int main()
{
    //display 即为 lambda 匿名函数的函数名
    auto display = [](int a,int b) -> void{cout << a << " " << b;};
    //调用 lambda 函数
    display(10,20);
    return 0;
}
```

程序执行结果为：

10 20

可以看到，程序中使用 auto 关键字为 lambda 匿名函数设定了一个函数名，由此我们即可在作用域内调用该函数。

【例 2】值传递和引用传递的区别

```

#include <iostream>
using namespace std;
//全局变量
int all_num = 0;
int main()
{
    //局部变量
    int num_1 = 1;
    int num_2 = 2;
    int num_3 = 3;
    cout << "lambda1:\n";
    auto lambda1 = [=]{
        //全局变量可以访问甚至修改
        all_num = 10;
        //函数体内只能使用外部变量，而无法对它们进行修改
        cout << num_1 << " "
             << num_2 << " "
             << num_3 << endl;
    };
    lambda1();
    cout << all_num <<endl;

    cout << "lambda2:\n";
    auto lambda2 = [&]{
        all_num = 100;
        num_1 = 10;
        num_2 = 20;
        num_3 = 30;
        cout << num_1 << " "
             << num_2 << " "
             << num_3 << endl;
    };
    lambda2();
    cout << all_num << endl;
    return 0;
}
```

程序执行结果为：

lambda1:
1 2 3
10
lambda2:
10 20 30
100

可以看到，在创建 lambda1 和 lambda2 匿名函数的作用域中，有 num_1、num_2 和 num_3 这 3 个局部变量，另外还有 all_num 全局变量。

其中，lambda1 匿名函数是以 [=] 值传递的方式导入的局部变量，这意味着默认情况下，此函数内部无法修改这 3 个局部变量的值，但全局变量 all_num 除外。相对地，lambda2 匿名函数以 [&] 引用传递的方式导入这 3 个局部变量，因此在该函数的内部不就可以访问这 3 个局部变量，还可以任意修改它们。同样，也可以访问甚至修改全局变量。

> 感兴趣的读者，可自行尝试在 lambda1 匿名函数中修改 num_1、num_2 或者 num_3 的值，观察编译器的报错信息。

当然，如果我们想在 lambda1 匿名函数的基础上修改外部变量的值，可以借助 mutable 关键字，例如：

```

auto lambda1 = [=]() mutable{
    num_1 = 10;
    num_2 = 20;
    num_3 = 30;
    //函数体内只能使用外部变量，而无法对它们进行修改
    cout << num_1 << " "
         << num_2 << " "
         << num_3 << endl;
};
```

由此，就可以在 lambda1 匿名函数中修改外部变量的值。但需要注意的是，这里修改的仅是 num_1、num_2、num_3 拷贝的那一份的值，真正外部变量的值并不会发生改变。

【例 3】执行抛出异常类型

```

#include <iostream>
using namespace std;
int main()
{
    auto except = []()throw(int) {
        throw 10;
    };
    try {
        except();
    }
    catch (int) {
        cout << "捕获到了整形异常";
    }
    return 0;
}
```

程序执行结果为：

捕获到了整形异常

可以看到，except 匿名数组中指定函数体中可以抛出整形异常，因此当函数体中真正发生整形异常时，可以借助 try-catch 块成功捕获并处理。

在此基础上，在看一下反例：

```

#include <iostream>
using namespace std;
int main()
{
    auto except1 = []()noexcept{
        throw 100;
    };

    auto except2 = []()throw(char){
        throw 10;
    };
    try{
        except1();
        except2();
    }catch(int){
        cout << "捕获到了整形异常"<< endl;
    }

    return 0;
}
```

此程序运行会直接崩溃，原因很简单，except1 匿名函数指定了函数体中不发生任何异常，但函数体中却发生了整形异常；except2 匿名函数指定函数体可能会发生字符异常，但函数体中却发生了整形异常。由于指定异常类型和真正发生的异常类型不匹配，导致 try-catch 无法捕获，最终程序运行崩溃。

> 如果不使用 noexcept 或者 throw()，则 lambda 匿名函数的函数体中允许发生任何类型的异常。