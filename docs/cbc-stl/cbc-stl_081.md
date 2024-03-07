# C++ sort（STL sort）排序算法详解

在很多应用中，排序都是至关重要的，而且很多 STL 算法也只适用于有序对象序列。定义在 algorithm 头文件中的函数模板 sort<Iter>() 默认会将元素段排成升序，这也就意味着排序的对象的类型需要支持 `<` 运算符。 

对象也必须是可交换的，这说明可以用定义在 utility 头文件中的函数模板 swap() 来对两个对象进行交换。这进一步表明这种对象的类型需要实现移动构造函数和移动赋值运算符。

函数模板 sort<Iter>() 的类型参数 Iter 是元素段元素对应的迭代器类型，而且它们必须支持随机访问迭代器。这表明 sort() 算法只能对提供随机访问迭代器的容器中的元素进行排序，也说明 sort() 只能接受 array、vector、deque 或标准数组中的元素。可以回顾前面章节，list 和 forward_list 容器都有成员函数 sort(); 这些用来排序的特殊成员函数是必要的，因为 list 只提供双向迭代器，且 forward_list 只提供正向迭代器。

可以从函数调用的参数中推导出 sort() 的模板类型参数，它们是定义被排序对象范围的迭代器。当然，迭代器类型隐式定义了这个元素段中对象的类型。下面是一个使用 sort() 算法的示例：

```
std::vector<int> numbers {99, 77, 33, 66, 22, 11, 44, 88};
std::sort(std::begin(numbers), std::end(numbers));
std::copy(std::begin(numbers), std::end(numbers),std:: ostream_iterator<int> {std::cout," "}) ;
// Output: 11 22 33 44 66 77 88 99
```

sort() 调用将 number 容器的全部元素排成升序，然后用 copy() 算法输出结果。可以不必对容器的全部内容进行排序。下面这条语句对 numbers 中除了第一个和最后一个元素之外的元素进行了排序：

```
std::sort(++std::begin(numbers),--std::end(numbers));
```

为了将元素排成降序，需要提供一个用来比较元素的函数对象，作为 sort() 的第三个参数：

```
std::sort(std::begin(numbers), std::end(numbers), std::greater<>());
```

这个比较函数必须返回布尔值，而且有两个参数，它们要么是迭代器解引用后得到的类型，要么是迭代器解引用后可以隐式转换成的类型。参数可以是不同类型的。只要比较函数满足这些条件，它就可以是你喜欢的任何样子，也包括 lambda 表达式。例如：

```
std::deque<string> words { "one", "two", "nine", "nine", "one", "three", "four", "five", "six" };
std::sort(std::begin(words), std::end(words),[](const string& s1, const string& s2) { return s1.back()> s2.back();});
std::copy(std::begin(words), std::end(words),
std::ostream_iterator<string> {std::cout," "}); // six four two one nine nine one three five
```

这段代码对 deque 容器 words 中的 string 元素进行了排序，并且输出了排序后的结果。这里的比较函数是一个 lambda 表达式，它们用每个单词的最后一个字母来比较排序的顺序。结果元素以它们最后一个字母的降序来排序。

下面在一个简单的示例中介绍 sort() 的用法。这里会先从键盘读取 Name 对象，然后将它们按升序排列，再输出结果。Name 类定义在 Name.h 头文件中，它包含下面这些代码：

```
#ifndef NAME_H
#define NAME_H
#include <string>                                // For string class

class Name
{
private:
    std::string first {};
    std::string second {};

public:
    Name(const std::string& name1, const std::string& name2) : first(name1), second(name2){}
    Name()=default;
    std::string get_first() const { return first; }
    std::string get_second() const { return second; }

    friend std::istream& operator>>(std::istream& in, Name& name);
    friend std::ostream& operator<<(std::ostream& out, const Name& name);
};

// Stream input for Name objects
inline std::istream& operator>>(std::istream& in, Name& name)
{
    return in >> name.first >> name.second;
}

// Stream output for Name objects
inline std::ostream& operator<<(std::ostream& out, const Name& name)
{
    return out << name.first << " " << name.second;
}
#endif
```

这个流插入和提取运算符被定义为 Name 对象的友元函数。可以将 operator<() 定义为类的成员函数，但为了展示如何为 sort() 算法指定比较函数参数，这里没有定义它。下面是程序代码：

```
// Sorting class objects
#include <iostream>                              // For standard streams
#include <string>                                // For string class
#include <vector>                                // For vector container
#include <iterator>                              // For stream and back insert iterators
#include <algorithm>                             // For sort() algorithm
#include "Name.h"

int main()
{
    std::vector<Name> names;
    std::cout << "Enter names as first name followed by second name. Enter Ctrl+Z to end:";
    std::copy(std::istream_iterator<Name>(std::cin), std::istream_iterator<Name>(),std::back_insert_iterator<std::vector<Name>>(names));

    std::cout << names.size() << " names read. Sorting in ascending sequence...\n";
    std::sort(std::begin(names), std::end(names), [](const Name& name1, const Name& name2) {return name1.get_second() < name2.get_second(); });

    std::cout << "\nThe names in ascending sequence are:\n";
    std::copy(std::begin(names), std::end(names), std::ostream_iterator<Name>(std::cout, "\n"));
}
```

main() 中的一切几乎都是使用 STL 模板完成的。names 容器用来保存从 cin 读入的姓名。输入由 copy() 算法执行，它使用一个 istream_iterator<Name> 实例读入 Name 对象。 istream_iterator<Name> 默认的构造函数会创建流的结束迭代器。copy() 函数用 back_insert_iterator<Name>() 创建的 back_inserter<Name> 迭代器将输入的每个对象复制到 names 中。为 Name 类重载的流运算符允许使用流迭代器来输入和输出 Name 对象。

Name 对象的比较函数是用 lambda 表达式定义的，它是 sort() 算法的第三个参数。如果想将 operator<() 定义为 Name 类的成员函数，可以省略这个参数。然后用 copy() 算法将排序后的 names 写入标准输出流，它会将前两个参数指定范围内的元素复制到作为第三个参数的 ostream_iterator<Name> 对象中。

下面是示例输出：

Enter names as first name followed by second name. Enter Ctrl+Z to end:
Jim Jones
Bill Jones
Jane Smith
John Doe
Janet Jones
Willy Schaferknaker
^Z
6 names read. Sorting in ascending sequence...

The names in ascending sequence are:
John Doe
Jim Jones
Bill Jones
Janet Jones
Willy Schaferknaker
Jane Smith

对 names 的排序只考虑了姓。当姓相同时，可以将 lambda 表达式扩展为可以比较名。你可能会好奇在这个示例中为什么不用 pair<string,string> 来表示姓名，这比定义一个新的类要简单多了。显然我们可以这么做，但却没有定义类这么清楚明了。