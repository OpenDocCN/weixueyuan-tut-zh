# C++ string 支持配置器方法详解

配置器是 STL 的重要内容。使用 STL 必然会涉及容器，而容器中存储了大量的数值，必然需要分配内存空间。配置器的作用就是为容器分配内存。

配置器最早是为将内存模型抽象化而提出的。所以使用内存配置器分配内存时，是按对象的个数进行的，而不是按字节数。这有别于原来的 new [] 和 new 操作符。配置器最大的优点在于，配置器实现了将算法、容器与物理存储细节分隔。配置器可以提供一套分配与释放内存的标准方式，并提供用作指针类型和引用类型的标准名称。目前而言，配置器仅是一种纯粹的抽象。行为上类似分配器的类型都可看作配置器。

C++ STL 提供了标准分配器，目的是为用户提供更多的服务。basic_string 模板以及 string 类均提供了对常见配置器的相关支持。basic_string 类模板中包含 1 个配置器类型的成员 allocator_type。对于 string 对象，allocator_type 可以作为配置器类的对象使用；对 string 类而言，allocator_type 等价于 allocator<char>，即分配数据类型为 char 的内存，便于 string 类的对象存储 char 型字符。

同时 basic_string 类模板的第 3 个参数也是配置器模板参数。basic_string 类模板的形式如下：

template <class CharType, class Traits = char_traits <CharType>, class Allocator=allocator<CharType>> class basic_string

而 string 类的声明形式如下：

typedef basic_string <char> string;

对于 basic_string 类模板，其第 1 个参数是 CharType，第 2 个参数和第 3 个参数的默认值和 CharType 均相关。在声明 string 类时，参数 char 取代模板中的 CharType，string 即成为模板的实例，同时模板中的第 3 个参数成为 "class Allocator = allocator <char>"，其意义为 string 中对象的内存类型为 char 型。

string 类还提供了 1 个和配置器相关的函数 get_allocator()，其函数原型为：

allocator_type string:: get_allocator () const

函数返回 string 类的内存模型对象，可以用于构造新的字符串。以如下程序为例介绍该函数的使用方法。

```
#include <iostream>
#include <string>
#include <memory>
using namespace std;
int main ()
{
    string s("abed");
    basic_string <char> s1 (s.get_allocator());
    basic_string <char> :: allocator_type aT = s1.get_allocator ();
    string::size_type se = s1.size ();
    cout << se << endl;
}
```

由于在 string 类中，allocator 是保护成员，难以直接调用对内存的直接配置。对于配置器，一般情况下都是使用默认配置器。对于本节内容，读者了解即可。