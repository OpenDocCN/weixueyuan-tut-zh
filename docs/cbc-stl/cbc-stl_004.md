# C++ basic_string（字符串类模板）详解

"字符" 本身是个有趣的抽象概念。例如，在纸上或者屏幕上，字符 "C" 仅仅是一段曲线而已。在计算机中，用一个 8 Byte 存储该字符，并赋值 67；字符 "C" 还是拉丁字母的第三个字母；在化学专业，字符 "C" 是原子碳的缩写形式；在计算机学科中，字符 "C" 又被用来表示一种程序设计语言的名字。

目前，在计算机领域，字符集合是在字符与整数值之间的一种映射关系。

C++ 程序员通常假定能够使用美国字符集（ASCII），但 C++ 允许程序员缺少某些字符的可能性。如果在程序开发过程中，源代码含有 ASCII 里所没有的字符，这是比较麻烦的，许多语言（例如中文、丹麦文、法文、冰岛文、日文）无法用 ASCII 中的字符正常写出来，即使扩充到 16 位字符集，也无法将人类所知的所有字符放在同一字符集中。据说已经出现的 32 位字符集能保存每一个字符，但因字符数量庞大，不便于使用，尚未得到推广。

C++ 语言允许程序员使用任何字符集作为字符串的字符，也允许程序员使用扩充字符集或可移植的数值编码。从原则上讲，字符串能以任何（带有正确的复制操作）类型作为其字符类型。标准字符串类 string 要求其中的字符不能包含用户自定义复制操作，有助于字符串 I/O 的简化与高效率。

字符类型的性质是由字符特征类（char_traits）定义的。字符特征类是下述模板的特例：

template <class Ch> struct char_traits { }

所有字符特征类均定义在名称空间 std 中，标准的字符特征类由头文件 <String> 给出。通用字符串特征类 char_traits 本身不具有任何属性，只有针对特定字符类型的专门 char_traits 才具有属性。

标准字符串模板的实例类 basic_string 依赖于诸多类型和函数。若一个类型作为 basic_string 字符类型，必须提供支持上述功能的字符特征类（char_traits）。

C++ 的字符串模板实例化类 basic_string 中，还集合了大量的字符串处理函数，与标准容器类似。该类模板的声明如下：

```
template <class Ch, class Tr = char_traits <Ch>, class A=allocator <Ch>> class std::basic_string
{
    public:
    ...
}
```

在上述模板声明中，第一个参数（class Ch）是说明单个字符（Ch）所属型别（class）。

第二个参数（class Tr = char_traits <Ch>）是特性类别，用以提供字符串类别中的所有字符核心操作。该特性类别规定了“复制字符”或“比较字符”的做法；如果不指定该特性类别，系统会根据现有的字符型别采用默认的特性类别。

第三个参数带有默认值（class A = allocator<Ch>），用以定义字符串类别所采用的内存模式，通常设定为“默认内存模型 allocator”。该模板及其相关功能都定义在名称空间 std 中，由头文件 <string> 给出，其中包含了两个定义类型，可以为最常用的串类型提供便于使用的名称，即 C++ STL 提供了两个 basic_string<> 实例化版本：

typedef basic_string <char> string;
typedef basic_string <wchar> wstring;

其中，wstring 类是为了便于使用宽字符集，例如 Unicode 或某些欧洲字符集。但所有字符串类型均使用相同接口，其用法和问题是相同的。在本教程中，仍以 string 表示任何字符串型别。

basic_string 和 vector 类似，而 basic_string 还提供典型的字符串操作，例如子串检索。basic_string 没有提供一组完整的操作函数。通常 string 不能直接使用数组或者 vector，为了更好地支持 string 的常见应用，程序员在实现过程中需要尽量减少复制。尤其对于较短的字符串，不应使用自由存储空间，但允许对较长的字符串进行简单修改。

basic_string<T> 没有虚函数，这点和其他标准库类型一致。当需要设计更复杂的文字处理类时，可考虑用它加以实现。

与其他标准容器相似，basic_string 提供了一组成员类型名，程序员能使用这些与串相关的类型。例如：

typedef Tr traits_type;
typedef typename Tr::char_type value_type
typedef A allocator_type
……

basic_string 除支持最简单的 basic_string <char> 之外，还支持许多不同种类的字符串，例如：

typedef basic_string <unsigned char> Ustring;
tyepdef basic_string <Jchar> Jstring; //日文字符串

无论如何定义字符串，模板 basic_string 的大量函数均可便捷地使用。模板 basic_string<Ch> 能够存放集合 Ch 中的任何字符，特别是 string 中的 '0'。"字符类型" Ch 的行为必须像字符，但它不能包含用户确定的复制构造函数、析构函数和复制赋值。

虽然字符串类 string 包含了诸多的成员和函数，但个别功能没能够实现，例如正则表达式和较复杂的文本处理功能。

总体而言，string 类似的字符串操作逐渐变得简单了。程序员可以定义 string 类型的对象、string 类的重载操作符和成员函数，这使字符串操作变得非常容易。