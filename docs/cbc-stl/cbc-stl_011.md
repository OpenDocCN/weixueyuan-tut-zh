# C++字符串输入输出操作详解

"<<" 和 ">>" 提供了 C++ 语言的字符串输入和字符串输出功能。"<<" 可以将字符读入一个流中（例如 ostream）；">>" 可以实现将以空格或回车为 "结束符" 的字符序列读入到对应的字符串中，并且开头和结尾的空白字符不包括进字符串中。

还有一个常用的 getline() 函数，该函数的原型包括两种形式：

template <class CharType, class Traits, class Allocator > basic_istream<CharType, Traits>& getline (basic_istream<CharType, Traits>& _Istr,basic_string <CharType，Traits, Allocator> &_Str);
//上述原型包含 2 个参数：第 1 个参数是输入流；第 2 个参数是保存输入内容的字符串
template <class CharType, class Traits, class Allocator> basic_istream<CharType, Traits>& getline (basic_istream <CharType, Traits>&_ Istr, basic_string <CharType, Traits, Allocator>& _Str,CharType_Delim);
//上述原型包含 3 个参数：第 1 个参数是输入流，第 2 个参数保存输入的字符串，第 3 个参数指定分界符。

该函数可将整行的所有字符读到字符串中。在读取字符时，遇到文件结束符、分界符、回车符时，将终止读入操作，且文件结束符、分界符、回车符在字符串中不会保存；当已读入的字符数目超过字符串所能容纳的最大字符数时，将会终止读入操作。

下面分别按上述两种函数原型举例说明，参见下述程序：

```
#include <iostream>
#include <string>
using namespace std;
void main ()
{
string s1, s2;
getline(cin, s1);
getline(cin, s2, ' ');
cout << "You inputed chars are: " << s1 << endl;
cout << "You inputed chars are: " << s2 << endl;
}
```

程序的执行结果为：

123456
asdfgh klj
You inputed chars are: 123456
You inputed chars are: asdfgh

注意，程序中输入的第二行字符中间包含空格字符，而空格之后的字符没有被存储到字符串 s2 中。