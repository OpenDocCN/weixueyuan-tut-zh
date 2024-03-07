# C++11 long long 超长整形详解

> 原文：[`c.biancheng.net/view/7809.html`](http://c.biancheng.net/view/7809.html)

C++ 11 标准中，基于整数大小的考虑，共提供了如表 1 所示的这些数据类型。与此同时，标准中还明确限定了各个数据类型最少占用的位数。

表 1 C++11 标准中所有的整形数据类型

| 整数类型 | 等价类型 | C++11 标准规定占用最少位数 |
| short | short int（有符号短整型） | 至少 16 位（2 个字节） |
| signed short |
| signed short int |
| unsigned short | unsigned short int（无符号短整型） |
| unsigned short int |
| int | int（有符号整形） | 至少 16 位（2 个字节） |
| signed |
| signed int |
| unsigned | unsigned int（无符号整形） |
| unsigned int |
| long | long int（有符号长整形） | 至少 32 位（4 个字节） |
| long int |
| signed long |
| signed long int |
| unsigned long | unsigned long int（无符号长整形） |
| unsigned long int |
| long long（C++11） | long long int（有符号超长整形） | 至少 64 位（8 个字节） |
| long long int（C++11） |
| signed long long（C++11） |
| signed long long int（C++11） |
| unsigned long long（C++11） | unsigned long long int（无符号超长整型） |
| unsigned long long int（C++11） |

> C++11 标准规定，每种整数类型必须同时具备有符号（signed）和无符号（unsigned）两种类型，且每种具体的有符号整形和无符号整形所占用的存储空间（也就是位数）必须相同。注意，C++11 标准中只限定了每种类型最少占用多少存储空间，不同的平台可以占用不同的存储空间。

在表 1 罗列的这些数据类型中，long long 超长整型是 C++ 11 标准新添加的，接下来就对该整数类型做具体的介绍。

说道 C++ 标准委员会将 long long 整形写入 C++ 11 标准中，其实早在 1995 年，就有人提议将 long long 整形写入 C++ 98 标准，但被委员会拒绝了。而后 long long 整形被 C99 标准（C 语言标准之一）采纳，并逐渐被很多编译器支持，于是 C++ 标准委员会重新决定将 long long 整形写入 C++ 11 标准中。

如同 long 类型整数需明确标注 "L" 或者 "l" 后缀一样，要使用 long long 类型的整数，也必须标注对应的后缀：

*   对于有符号 long long 整形，后缀用 "LL" 或者 "ll" 标识。例如，"10LL" 就表示有符号超长整数 10；
*   对于无符号 long long 整形，后缀用 "ULL"、"ull"、"Ull" 或者 "uLL" 标识。例如，"10ULL" 就表示无符号超长整数 10；

> 如果不添加任何标识，则所有的整数都会默认为 int 类型。

对于任意一种数据类型，读者可能更关心的是此类型的取值范围。对于 long long 类型来说，如果想了解当前平台上 long long 整形的取值范围，可以使用`<climits>`头文件中与 long long 整形相关的 3 个宏，分别为 LLONG_MIN、LLONG_MAX 和 ULLONG_MIN：

1.  LLONG_MIN：代表当前平台上最小的 long long 类型整数；
2.  LLONG_MAX：代表当前平台上最大的 long long 类型整数；
3.  ULLONG_MIN：代表当前平台上最大的 unsigned long long 类型整数（无符号超长整型的最小值为 0）；

举个例子：

```

#include <iostream>
#include <iomanip>
#include <climits>
using namespace std;

int main()
{
    cout <<"long long 最大值：" << LLONG_MIN <<" "<< hex << LLONG_MIN <<"\n";
    cout << dec <<"long long 最小值：" << LLONG_MAX << " " << hex << LLONG_MAX << "\n";
    cout << dec << "unsigned long long 最大值：" << ULLONG_MAX << " " << hex << ULLONG_MAX;
    return 0;
}
```

程序执行结果为（不唯一）：

long long 最大值：-9223372036854775808 8000000000000000
long long 最小值：9223372036854775807 7fffffffffffffff
unsigned long long 最大值：18446744073709551615 ffffffffffffffff

> 关于整形在内存中到底是如何存储的，读者可阅读《整数在内存中是如何存储的，为什么它堪称天才般的设计》一节。

此程序中，输出了各最大值和最小值对应的十六进制，显然在当前平台（Windows10 64 位操作系统）上，long long 超长整型占用 64 位（也就是 16 个字节）的存储空间。读者可自行在自己的机器上运行此段代码，即可轻松得知 long long 类型在自己机器上所占用的字节数。