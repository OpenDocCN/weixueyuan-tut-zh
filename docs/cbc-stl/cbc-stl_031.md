# C++ deque 修改（替换、更改）元素详解

deque 的成员函数 assign() 可以替换现有的所有元素。它有三个重版版本；替换的新内容可以是由初始化列表指定的元素，也可以是由迭代器指定的一段元素，或是一个特定对象的多个副本。

这里展示了如何使用初始化列表来替换 deque 容器中的内容：

```
std::deque<std::string> words {"one", "two", "three", "four"};
auto init_list = {std::string{"seven"}, std::string{ "eight"}, std::string{"nine"}};
words.assign(init_list);
```

最后一条语句用 init_list 中的 string 对象替换掉了 words 中的元素。注意，这里不能直接把字符放入初始化列表。如果这么做，init_list 的类型会被推导为 initializer_list<const char*>，然而 assign() 需要的是一个 initializer_list<string> 类型的实参，这样就无法通过编译。当然，也可以不单独定义 init_list，可以在 assign() 的实参中定义初始化列表，例如：

```
words.assign({"seven”，"eight","nine"});
```

因为 words 的成员函数 assign() 需要一个 initializer_list<string> 类型的实参，编译器会用字符串列表生成一个这种类型的初始化列表。为了给 deque 容器赋值，需要提供两个迭代器作为参数：

```
std::vector<std::string> wordset {"this","that","these","those"};
words.assign(std::begin(wordset)+1, --std::end(wordset));
//Assigns "that" and "these"
```

assign() 函数只需要输入迭代器，因此可以使用任何类别的迭代器。最后一种可能是，用重复的对象来替换容器中的内容：

```
words.assign(8,"eight"); //Assign eight instances of string("eight")
```

第一个参数指定了替换当前容器内容的第二个参数的个数。vector 也提供了一套同样的 assign() 函数，所以可以更换一套新的 vector 容器元素。也可以使用赋值操作符来替换赋值运算符左边的 deque 容器的内容。赋值运算的右操作数必须和左操作数是相同类型，也可以是一个初始化列表。vector 容器同样也支持这些操作。下面是一个为 deque 替换一套新元素的示例：

```
std::deque<std::string> words {"one","two","three", "four"};
std::deque<std::string> other_words;
other_words = words; // other_words same contents as words
words = {"seven", "eight", "nine"}; // words contents replaced
```

执行完这些语句后，otlier_words 会包含和 words 相同的元素，words 则包含初始化列表中的那些元素。赋完值后，容器的大小会反映赋值元素的个数。为一个 vector 容器替换一套新的元素(来自于同类型的 vector 容器或初始化列表)，它的大小和这套新元素相同。

下面是一个使用 deque 容器的完整示例：

```
// Using a deque container
#include <iostream> // For standard streams
#include <algorithm> // For copy()
#include <deque> // For deque container
#include <string> // For string classes
#include <iterator> // For front_insert_iterator & stream iterators

using std::string;

int main()
{
    std::deque<string> names;
    std::cout << "Enter first names separated by spaces. Enter Ctrl+Z on a new line to end:\n";
    std::copy(std::istream_iterator < string > {std::cin}, std::istream_iterator < string > {}, std::front_inserter(names));
    std::cout << "\nIn reverse order, the names you entered are:\n";
    std::copy(std::begin(names), std::end(names), std::ostream_iterator < string > {std::cout, "  "});
    std::cout << std::endl;
}
```

运行结果为：

Enter first names separated by spaces. Enter Ctrl+Z on a new line to end:
Fred Jack Jim George Mary Zor Rosie
^Z

In reverse order, the names you entered are:
Rosie  Zor  Mary  George  Jim  Jack  Fred

该程序读入几个任意长度的字符串，然后把它们存储在 names 容器中。copy() 算法将从 istream_iterator<string> 获取到的序列，输入到 names 容器的 front_insert_itemtor 中，这个迭代器是由函数 front_inserter() 返回的。

copy() 的第一个参数是用来输入的开始迭代器，第二个参数是对应的结束迭代器。当使用键盘输入 `Ctrl+Z` 时，输入迭代器会对应为结束迭代器；如果是从文件流中读取数据，当读到 `EOF` 时，也会产生一个结束迭代器。因为 deque 容器有成员函数 push_front()，可以用来在序列的头部添加元素，所以我们这里可以使用 front_insert_iterator； front_insert_iterator 通过调用 push_front()在容器中添加元素，因此它适用于有成员函数 push_front() 的任何容器。

输出也是由 copy() 算法生成的。前两个参数是用来指定元素范围的迭代器，这些元素被复制到第三个参数所指定的位置。因为前两个参数正好是 deque 容器的开始和结束迭代器，因此会复制 deque 容器的全部元素。目的地是一个接收字符串对象的 ostream_iterator，它会将这些字符串对象写入标准输出流。