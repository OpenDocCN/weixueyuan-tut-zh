# C++ string 获取字符串元素：[]和 at()

在通常情况下，string 是 C++ 中的字符串。字符串是一种特殊类型的容器，专门用来操作字符序列。

字符串中元素的访问是允许的，一般可使用两种方法访问字符串中的单一字符：下标操作符[] 和 成员函数 at()。两者均返回指定的下标位置的字符。第 1 个字符索引（下标）为 0，最后的字符索引为 length()-1。

需要注意的是，这两种访问方法是有区别的：

*   下标操作符 [] 在使用时不检查索引的有效性，如果下标超出字符的长度范围，会示导致未定义行为。对于常量字符串，使用下标操作符时，字符串的最后字符（即 '\0'）是有效的。对应 string 类型对象（常量型）最后一个字符的下标是有效的，调用返回字符 '\0'。
*   函数 at() 在使用时会检查下标是否有效。如果给定的下标超出字符的长度范围，系统会抛出 out_of_range 异常。

【例 1】

```
#include <iostream>
#include <string>
int main()
{
    const std::string cS ("c.biancheng.net");
    std::string s ("abode");
    char temp =0;
    char temp_1 = 0;
    char temp_2 = 0;
    char temp_3 = 0;
    char temp_4 = 0;
    char temp_5 = 0;
    temp = s [2]; //"获取字符 'c'
    temp_1 = s.at(2); //获取字符 'c'
    temp_2 = s [s.length()]; //未定义行为，返回字符'\0'，但 Visual C++ 2012 执行时未报错
    temp_3 = cS[cS.length()]; //指向字符 '\0'
    temp_4 = s.at (s.length ()); //程序异常
    temp_5 = cS.at(cS.length ()); //程序异常
    std::cout << temp <<temp_1 << temp_2 << temp_3 << temp_4 << temp_5 << std::endl;
    return 0;
}
```

通过对上述代码的分析可知，要理解字符串的存取需要多实践、多尝试，并且要牢记基础知识和基本规则。

为修改 string 字符串的内容，下标操作符 [] 和函数 at() 均返回字符的“引用”。但当字符串的内存被重新分配以后，可能会发生执行错误。

【例 2】

```
#include <iostream>
#include <string>
int main()
{
    std::string s ("abode");
    std::cout << s << std::endl ;
    char& r = s[2] ; //建立引用关系
    char*p=&s[3]; //建立引用关系
    r='X' ;//修改内容
    *p='Y' ;//修改内容
    std::cout << s << std::endl; //输出
    s = "12345678"; //重新赋值
    r ='X'; //修改内容
    *p='Y'; //修改内容
    std::cout << s << std::endl; //输出
    return 0;
}
```

程序输出结果为：

abode
abXYe
12XY5678

在例 2 中，使用 Visual C++ 2012 编译器编译，字符串被重新赋值后，修改其中某位置字符的值，仍然成功。这与前面所述的“可能会发生执行错误”其实并不矛盾。因为，从意义上讲，字符串被重新赋值后，只是其原来的引用关系已经没有意义了。