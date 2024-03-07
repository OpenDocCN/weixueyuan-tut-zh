# C++ auto 和 decltype 的区别

> 原文：[`c.biancheng.net/view/7158.html`](http://c.biancheng.net/view/7158.html)

通过《C++ auto》和《C++ decltype》两节的学习，相信大家已经掌握了 auto 和 decltype 的语法规则以及使用场景，这节我们将 auto 和 decltype 放在一起，综合对比一下它们的区别，并告诉大家该如何选择。

## 语法格式的区别

auto 和 decltype 都是 C++11 新增的关键字，都用于自动类型推导，但是它们的语法格式是有区别的，如下所示：

auto varname = value;  //auto 的语法格式
decltype(exp) varname [= value];  //decltype 的语法格式

其中，varname 表示变量名，value 表示赋给变量的值，exp 表示一个表达式，方括号`[ ]`表示可有可无。

auto 和 decltype 都会自动推导出变量 varname 的类型：

*   auto 根据`=`右边的初始值 value 推导出变量的类型；
*   decltype 根据 exp 表达式推导出变量的类型，跟`=`右边的 value 没有关系。

另外，auto 要求变量必须初始化，也就是在定义变量的同时必须给它赋值；而 decltype 不要求，初始化与否都不影响变量的类型。这很容易理解，因为 auto 是根据变量的初始值来推导出变量类型的，如果不初始化，变量的类型也就无法推导了。

auto 将变量的类型和初始值绑定在一起，而 decltype 将变量的类型和初始值分开；虽然 auto 的书写更加简洁，但 decltype 的使用更加灵活。

请看下面的例子：

```

auto n1 = 10;
decltype(10) n2 = 99;

auto url1 = "http://c.biancheng.net/cplus/";
decltype(url1) url2 = "http://c.biancheng.net/java/";

auto f1 = 2.5;
decltype(n1*6.7) f2;
```

这些用法在前面的两节中已经进行了分析，此处就不再赘述了。

## 对 cv 限定符的处理

「cv 限定符」是 const 和 volatile 关键字的统称：

*   const 关键字用来表示数据是只读的，也就是不能被修改；
*   volatile 和 const 是相反的，它用来表示数据是可变的、易变的，目的是不让 CPU 将数据缓存到寄存器，而是从原始的内存中读取。

在推导变量类型时，auto 和 decltype 对 cv 限制符的处理是不一样的。decltype 会保留 cv 限定符，而 auto 有可能会去掉 cv 限定符。

以下是 auto 关键字对 cv 限定符的推导规则：

*   如果表达式的类型不是指针或者引用，auto 会把 cv 限定符直接抛弃，推导成 non-const 或者 non-volatile 类型。
*   如果表达式的类型是指针或者引用，auto 将保留 cv 限定符。

下面的例子演示了对 const 限定符的推导：

```

//非指针非引用类型
const int n1 = 0;

auto n2 = 10;
n2 = 99;  //赋值不报错

decltype(n1) n3 = 20;
n3 = 5;  //赋值报错

//指针类型
const int *p1 = &n1;

auto p2 = p1;
*p2 = 66;  //赋值报错

decltype(p1) p3 = p1;
*p3 = 19;  //赋值报错
```

在 C++ 中无法将一个变量的完整类型输出，我们通过对变量赋值来判断它是否被 const 修饰；如果被 const 修饰那么赋值失败，如果不被 const 修饰那么赋值成功。虽然这种方案不太直观，但也是能达到目的的。

n2 赋值成功，说明不带 const，也就是 const 被 auto 抛弃了，这验证了 auto 的第一条推导规则。p2 赋值失败，说明是带 const 的，也就是 const 没有被 auto 抛弃，这验证了 auto 的第二条推导规则。

n3 和 p3 都赋值失败，说明 decltype 不会去掉表达式的 const 属性。

## 对引用的处理

当表达式的类型为引用时，auto 和 decltype 的推导规则也不一样；decltype 会保留引用类型，而 auto 会抛弃引用类型，直接推导出它的原始类型。请看下面的例子：

```

#include <iostream>
using namespace std;

int main() {
    int n = 10;
    int &r1 = n;

    //auto 推导
    auto r2 = r1;
    r2 = 20;
    cout << n << ", " << r1 << ", " << r2 << endl;

    //decltype 推导
    decltype(r1) r3 = n;
    r3 = 99;
    cout << n << ", " << r1 << ", " << r3 << endl;

    return 0;
}
```

运行结果：
10, 10, 20
99, 99, 99

从运行结果可以发现，给 r2 赋值并没有改变 n 的值，这说明 r2 没有指向 n，而是自立门户，单独拥有了一块内存，这就证明 r 不再是引用类型，它的引用类型被 auto 抛弃了。

给 r3 赋值，n 的值也跟着改变了，这说明 r3 仍然指向 n，它的引用类型被 decltype 保留了。

## 总结

auto 虽然在书写格式上比 decltype 简单，但是它的推导规则复杂，有时候会改变表达式的原始类型；而 decltype 比较纯粹，它一般会坚持保留原始表达式的任何类型，让推导的结果更加原汁原味。

从代码是否健壮的角度考虑，我推荐使用 decltype，它没有那么多是非；但是 decltype 总是显得比较麻烦，尤其是当表达式比较复杂时，例如：

vector<int> nums;
decltype(nums.begin()) it = nums.begin();

而如果使用 auto 就会清爽很多：

vector<int> nums;
auto it = nums.begin();

在实际开发中人们仍然喜欢使用 auto 关键字（我也这么干），因为它用起来简单直观，更符合人们的审美。如果你的表达式类型不复杂，我还是推荐使用 auto 关键字，优雅的代码总是叫人赏心悦目，沉浸其中。