# C++字符串查找函数详解

在 C 语言和 C++ 语言中，可用于实现字符串查找功能的函数非常多。在 STL 中，字符串的查找功能可以实现多种功能，比如说：

*   搜索单个字符、搜索子串；
*   实现前向搜索、后向搜索；
*   分别实现搜索第一个和最后一个满足条件的字符（或子串）；

若查找 find() 函数和其他函数没有搜索到期望的字符（或子串），则返回 npos；若搜索成功，则返回搜索到的第 1 个字符或子串的位置。其中，npos 是一个无符号整数值，初始值为 -1。当搜索失败时， npos 表示“没有找到（not found）”或“所有剩佘字符”。

值得注意的是，所有查找 find() 函数的返回值均是 size_type 类型，即无符号整数类型。该返回值用于表明字符串中元素的个数或者字符在字符串中的位置。

下面分别介绍和字符查找相关的函数。

## find()函数和 rfind()

find() 函数的原型主要有以下 4 种：

size_type find (value_type _Chr, size_type _Off = 0) const;
//find()函数的第 1 个参数是被搜索的字符、第 2 个参数是在源串中开始搜索的下标位置
size_type find (const value_type* _Ptr , size_type _Off = 0) const;
//find()函数的第 1 个参数是被搜索的字符串，第 2 个参数是在源串中开始搜索的下标位置
size_type find (const value_type* _Ptr, size_type _Off = 0, size_type _Count) const;
//第 1 个参数是被搜索的字符串，第 2 个参数是源串中开始搜索的下标，第 3 个参数是关于第 1 个参数的字符个数，可能是 _Ptr 的所有字符数，也可能是 _Ptr 的子串宇符个数
size_type find (const basic_string& _Str, size_type _Off = 0) const;
//第 1 个参数是被搜索的字符串，第 2 参数是在源串中开始搜索的下标位置

rfind() 函数的原型和 find()函数的原型类似，参数情况也类似。只不过 rfind() 函数适用于实现逆向查找。

find() 函数和 rfind() 函数的使用方法参见如下程序：

```
#include <iostream>
#include <string>
using namespace std;
int main ()
{
    string str_ch (" for");
    string str (" Hi, Peter, I'm sick. Please bought some drugs for me.");
    string::size_type m= str.find ('P', 5);
    string::size_type rm= str.rfind('P', 5);
    cout << "Example - find() : The position (forward) of 'P' is: " << (int) m << endl;
    cout << "Example - rfind(): The position (reverse) of 'P' is: " << (int) rm << endl;
    string::size_type n = str.find (" some", 0);
    string::size_type rn = str.rfind (" some", 0);
    cout << "Example - find () : The position (forward) of 'some' is: " << (int) n << endl;
    cout << "Example - rfind () : The position (reverse) of 'some' is: " << (int) rn << endl;
    string::size_type mo = str.find (" drugs", 0, 5);
    string::size_type rmo = str.rfind (" drugs", 0, 5);
    cout << "Example - find(): The position (forward) of 'drugs' is: " << (int) mo << endl;
    cout << "Example - rfind(): The position (reverse) of 'drugs' is: " << (int) rmo << endl;
    string::size_type no = str.find (str_ch, 0);
    string::size_type rno = str.rfind(str_ch, 0);
    cout << "Example - find (): The position of 'for' is: " << (int) no << endl;
    cout << "Example - rfind(): The position of 'for' is: " << (int) rno << endl;
    cin.get ();
}
```

程序的运行结果为：

Example - find() : The position (forward) of 'P' is: 5
Example - rfind(): The position (reverse) of 'P' is: 5
Example - find () : The position (forward) of 'some' is: 35
Example - rfind () : The position (reverse) of 'some' is: -1
Example - find(): The position (forward) of 'drugs' is: 40
Example - rfind(): The position (reverse) of 'drugs' is: -1
Example - find (): The position of 'for' is: 46
Example - rfind(): The position of 'for' is: -1

## find_first_of()函数和 find_last_of()函数

find_first_of() 函数可实现在源串中搜索某字符串的功能，该函数的返回值是被搜索字符串的第 1 个字符第 1 次出现的下标（位置）。若查找失败，则返回 npos。

find_last_of() 函数同样可实现在源串中搜索某字符串的功能。与 find_first_of() 函数所不同的是，该函数的返回值是被搜索字符串的最后 1 个字符的下标（位置）。若查找失败，则返回 npos。

上述两个函数的原型分别为：

size_type find_first_not_of (value_type_Ch, size_type_Off = 0) const; size_type find_first_of (const value_type* _Ptr, size_type _Off = 0) const;
size_type find_first_of (const value_type* _Ptr, size_type_Off, size_type_Count) const;
size_type find_first_of (const basic_string & _Str, size_type_Off = 0) const;
size_type find_last_of (value_type _Ch, size_type_Off = npos) const;
size_type find_last_of (const value_type* _Ptr, size_type_Off = npos) const;
size_type find_last_of (const value_type* _Ptr, size_type _Off, size_type _Count) const;
size_type find_last_of (const basic_string& _Str, size_type_Off = npos) const;

下面的程序示例详细阐述了 find_first_of() 函数和 find_last_of() 函数的使用方法。这两个函数和 find() 函数及 rfind() 函数的使用方法相同，具体参数的意义亦相同。

```
#include <iostream>
#include <string>
using namespace std;
int main ()
{
    string str_ch ("for");
    string str("Hi, Peter, I'm sick. Please bought some drugs for me. ");
    int length = str.length();
    string::size_type m = str.find_first_of ('P', 0);
    string::size_type rm = str.find_last_of ('P', (length - 1));
    cout << "Example - find_first_of (): The position (forward) of 'P' is: " << (int) m << endl;
    cout << "Example - find_last_of (): The position (reverse) of 'P' is： " << (int) rm << endl;
    string:: size_type n = str.find_first_of ("some", 0);
    string:: size_type rn = str.find_last_of ("some", (length -1));
    cout << "Example - find_first_of(): The position (forward) of 'some' is: " << (int) n << endl;
    cout << "Example - find_last_of(): The position (reverse) of 'some' is: " << (int) rn << endl;
    string:: size_type mo = str.find_first_of ("drugs", 0, 5);
    string:: size_type rmo = str.find_last_of ("drugs", (length-1), 5);
    cout << "Example - find_first_of () : The position (forward) of 'drugs' is: " << (int) mo << endl;
    cout << "Example - find_last_of () : The position (reverse) of 'drugs' is: " << (int) rmo << endl;
    string::size_type no = str.find_first_of (str_ch, 0);
    string::size_type rno = str.find_last_of (str_ch, (length -1));
    cout << "Example - find_first_of() : The position of 'for' is: " << (int) no << endl;
    cout << "Example - find_last_of () : The position of 'for' is: " << (int) rno << endl;
    cin.get();
    return 0;
}
```

程序执行结果：

Example - find_first_of (): The position (forward) of 'P' is: 4
Example - find_last_of (): The position (reverse) of 'P' is： 21
Example - find_first_of(): The position (forward) of 'some' is: 5
Example - find_last_of(): The position (reverse) of 'some' is: 51
Example - find_first_of () : The position (forward) of 'drugs' is: 8
Example - find_last_of () : The position (reverse) of 'drugs' is: 48
Example - find_first_of() : The position of 'for' is: 8
Example - find_last_of () : The position of 'for' is: 48

## find_first_not_of()函数和 find_last_not_of()函数

find_first_not_of() 函数的函数原型为：

size_type find_first_not_of (value_type _Ch, size_type_Off = 0) const;
size_type find_first_not_of (const value_type * _Ptr, size_type_Off = 0) const;
size_type find_first_not_of (const value_type* _Ptr, size_type_Off, size_type_Count) const;
size_type find_first_not_of (const basic_string & _Str, size_type_Off = 0) const;

find_first_not_of() 函数可实现在源字符串中搜索与指定字符（串）不相等的第 1 个字符；find_last_not_of() 函数可实现在源字符串中搜索与指定字符（串）不相等的最后 1 个字符。这两个函数的参数意义和前面几个函数相同，它们的使用方法和前面几个函数也基本相同。详见下面的程序：

```
#include < iostream >
#include <string>
using namespace std;
int main ()
{
    string str_ch (" for");
    string str ("Hi, Peter, I'm sick. Please bought some drugs for me.");
    int length = str.length ();
    string::size_type m= str.find_first_not_of ('P',0);
    string::size_type rm= str.find_last_not_of ('P', (length -1);
    cout << "Example - find_first_of (): The position (forward) of 'P' is: " << (int) m << endl;
    cout << "Example - find_last_of (): The position (reverse) of 'P' is: " << (int) rm << endl;
    string:: size_type n = str.find_first_not_of ("some", 0);
    string:: size_type rn = str.find_last_not_of ("some", (length -1));
    cout << "Example - find_first_of (): The position (forward) of 'some' is: " << (int) n << endl;
    cout << "Example - find_last_of (): The position (reverse) of 'some' is: " << (int) rn << endl;
    string:: size_type mo = str.find_first_not_of ("drugs", 0, 5);
    string:: size_type rmo = str.find_last_not_of ("drugs", (length-1), 5);
    cout << "Example - find_first_of (): The position (forward) of 'drugs' is: " << (int) mo << endl;
    cout << "Example - find_last_of (): The position (reverse) of 'drugs' is: " << (int) rno << endl;
    string::size_type no = str.find_first_not_of (str_ch, 0);
    string::size_type rno = str.find_last_not_of (str_ch, (length-1));
    cout << "Example - find_first_of (): The position of 'for' is: " << (int) no << endl;
    cout << "Example - find_last_of () : The position of 'for' is: " << (int) rno << endl;
    cin.get ();
    return 0;
}
```

程序运行结果为：

Example - find_first_of (): The position (forward) of 'P' is: 0
Example - find_last_of (): The position (reverse) of 'P' is: 52
Example - find_first_of (): The position (forward) of 'some' is: 0
Example - find_last_of (): The position (reverse) of 'some' is: 52
Example - find_first_of (): The position (forward) of 'drugs' is: 0
Example - find_last_of (): The position (reverse) of 'drugs' is: 52
Example - find_first_of (): The position of 'for' is: 0
Example - find_last_of () : The position of 'for' is: 52

本小节主要讲述 C++ STL 中的字符串查找函数。对于所述的 6 个查找函数，它们的使用形式大致相同，对于每个函数均配备了实例作为参考。请读者能认真对照例题，深刻理解这 6 个函数的使用方法，仔细体会函数每个参数的意义。