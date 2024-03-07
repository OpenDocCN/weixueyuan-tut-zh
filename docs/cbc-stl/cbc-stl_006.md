# C++ string 构造函数和析构函数详解

构造函数有四个参数，其中三个具有默认值。要初始化一个 string 类，可以使用 C 风格字符串或 string 类型对象，也可以使用 C 风格字符串的部分或 string 类型对象的部分或序列。

注意，不能使用字符或者整数去初始化字符串。

常见的 string 类构造函数有以下几种形式：

string strs //生成空字符串
string s(str) //生成字符串 str 的复制品
string s(str, stridx) //将字符串 str 中始于 stridx 的部分作为构造函数的初值
string s(str, strbegin, strlen) //将字符串 str 中始于 strbegin、长度为 strlen 的部分作为字符串初值
string s(cstr) //以 C_string 类型 cstr 作为字符串 s 的初值
string s(cstr,char_len)    //以 C_string 类型 cstr 的前 char_len 个字符串作为字符串 s 的初值
strings(num, c) //生成一个字符串，包含 num 个 c 字符
strings(strs, beg, end)    //以区间[beg, end]内的字符作为字符串 s 的初值

析构函数的形式如下：

~string() //销毁所有内存，释放内存

如果字符串只包含一个字符，使用构造函数对其初始化时，使用以下两种形式比较合理：

std::string s('x');    //错误
std::string s(1, 'x');    //正确

或

std::string s("x");    //正确

C_string 一般被认为是常规的 C++ 字符串。目前，在 C++ 中确实存在一个从 const char * 到 string 的隐式型别转换，却不存在从 string 对象到 C_string 的自动型别转换。对于 string 类型的字符串，可以通过 c_str() 函数返回该 string 类对象对应的 C_string。

通常，程序员在整个程序中应坚持使用 string 类对象，直到必须将内容转化为 char* 时才将其转换为 C_string。

【例 1】

```
#include <iostream>
#include <string>
using namespace std;

int main ()
{
    string str ("12345678");
    char ch[ ] = "abcdefgh";
    string a; //定义一个空字符串
    string str_1 (str); //构造函数，全部复制
    string str_2 (str, 2, 5); //构造函数，从字符串 str 的第 2 个元素开始，复制 5 个元素，赋值给 str_2
    string str_3 (ch, 5); //将字符串 ch 的前 5 个元素赋值给 str_3
    string str_4 (5,'X'); //将 5 个 'X' 组成的字符串 "XXXXX" 赋值给 str_4
    string str_5 (str.begin(), str.end()); //复制字符串 str 的所有元素，并赋值给 str_5
    cout << str << endl;
    cout << a << endl ;
    cout << str_1 << endl;
    cout << str_2 << endl;
    cout << str_3 << endl;
    cout << str_4 << endl;
    cout << str_5 << endl;
    return 0;
}

```

程序运行结果为：

12345678

12345678
34567
abcde
XXXXX
12345678

使用 cout 输出 string 类型对象 a 时，输出为空。这是因为没有给 string 类型对象 a 赋值。

通过上述内容的学习，读者应对 string 类的构造函数和析构函数有了初步了解，并能够使用构造函数创建 string 类型对象。