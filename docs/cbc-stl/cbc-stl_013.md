# C++ string 支持迭代器方法详解

理解迭代器是理解 STL 的关键所在。模板使得算法独立于存储的数据类型，而迭代器使得算法独立于使用的容器类型。

STL 定义了 5 种迭代器，根据所需的迭代器类型对算法进行描述。这 5 种迭代器分别是：输入迭代器、输出迭代器、正向迭代器、双向迭代器和随机访问迭代器。对于这 5 种迭代器不仅可以执行解除引用操作（* 操作符），还可进行比较。本节主要讲述 basic_string（或 string 类）中迭代器的使用。

basic.string 和 string 类均提供了常规的迭代器和反向迭代器。string 是字符的有序群集。

C++ 标准库为 string 提供了相应接口，便于将字符串作为 STL 容器使用。可以使用迭代器遍历 string 内的所有字符；也可以使用其他算法遍历 string 内的所有字符。而且能够访问字符串中的每个字符，并对这些字符进行排序、逆向重排、找出最大值等操作。

string 类的迭代器是随机存取迭代器，即支持随机存取。任何一个 STL 算法都可与其搭配使用。通常 string 的“迭代器型别”由 string class 本身定义，通常可以被简单地定义为一般指针。对迭代器而言，如果发生重新分配，或其所指的值发生某些变化时，迭代器会失效。

string 类中和使用迭代器相关的成员函数是很多的，主要包括 begin()、end()、rbegin ()、rend()、append()、assign()、insert()、erase()、replace() 等。

下面通过一个列子说明迭代器在 string 类中的使用方法。

```
#include <iostream>
#include <string>
#include <algorithm>
using namespace std;
int main ()
{
    string s ("The zip code of Hondelage in Germany is 38108.");
    cout << "Original: " << s << endl;
    string sd(s.begin(),s.end ()); //构造函数中使用迭代器
    cout << "Destination: " << sd << endl;
    transform (sd.begin(), sd.end(), sd.begin(), toupper); //算法中使用迭代器(仿函数)
    cout << "Destination (All Toupper)): " << sd << endl;
    string sd1;
    sd1.append (sd.begin(),(sd.end() -7)); //append()函数中使用迭代器
    cout << "Destination sd1: " << sd1 << endl;
    string sd2;
    string::reverse_iterator iterA;
    string temp = "0";
    for (iterA = sd.rbegin (); iterA != sd.rend (); iterA++) //reverse_iterator
    {
        temp=* iterA;
        sd2.append (temp);
    }
    cout << "Destination sd2: " << sd2 << endl;
    sd2.erase (0, 15); //erase()函数中使用迭代器
    cout << "Destination sd2 (Erased 15 chars) : " << sd2 << endl;
    string::iterator iterB = sd2.begin ();
    string sd3 = string ("12345678");
    sd2.insert (sd2.begin(), sd3.begin(), sd3.end()); //insert()函数中使用迭代器
    cout << "Destination sd2 (Insert 8 chars) : " << sd2 << endl;
    sd2.replace (sd2.begin (), sd2.end(), "This is an Exarrple of Replace"); //Replace
    cout <<"Destination sd2 (Replace All): " << sd2 << endl; // replace ()函数中使用迭代器
}
```

程序运行结果为：

Original: The zip code of Hondelage in Germany is 38108.
Destination: The zip code of Hondelage in Germany is 38108.
Destination (All Toupper)): THE ZIP CODE OF HONDELAGE IN GERMANY IS 38108.
Destination sd1: THE ZIP CODE OF HONDELAGE IN GERMANY IS
Destination sd2: .80183 SI YNAMREG NI EGALEDNOH FO EDOC PIZ EHT
Destination sd2 (Erased 15 chars) : EG NI EGALEDNOH FO EDOC PIZ EHT
Destination sd2 (Insert 8 chars) : 12345678EG NI EGALEDNOH FO EDOC PIZ EHT
Destination sd2 (Replace All): This is an Exarrple of Replace