# C++输入流迭代器（STL 输入流迭代器）详解

输入流迭代器是一个可以在文本模式下从流中提取数据的输入迭代器，这意味着不能用它处理二进制流。

一般用两个流迭代器来从流中读取全部的值：指向要读入的第一个值的开始迭代器，指向流的末尾的结束迭代器。在输入流的文件结束状态(End-Of-File，EOF)被识别时，就可以确定结束迭代器。

定义在 iterator 头文件中的 istream_iterator 模板会用提取运算符 >> 从流中读入 T 类型的值。对于这些工作，必须有一个从 istream 对象读取 T 类型值的 operator>>() 函数的重载版本。因为 istream_iterator 是一个输入迭代器，所以它的实例是一个单向迭代器；它只能被使用一次。默认情况下，我们认为这种流包含的是 char 类型的字符。

可以通过传入一个输入流对象到构造函数来生成一个 istream_iterator 对象。istream_iterator 对象有拷贝构造函数。下面是一个生成输入流迭代器的示例：

```
std::istream_iterator<string> in {std::cin}; // Reads strings from cin
std::istream_iterator<string> end_in;// End-of-stream iterator
```

默认构造函数会生成一个代表流结束的对象，也就是当 EOF 被识别时的那种对象。

虽然默认情况下，这种流被认为包含的是 char 类型的字符，但能够定义输入流迭代器来读取包含其他类型字符的流。例如，下面展示了如何定义输入流迭代器来读取一个包含 wchar_t 字符的流：

```
std::basic_ifstream<wchar_t> file_in{"no_such_file.txt"};// File stream of wchar_t
std::istream_iterator<std::wstring, wchar_t> in {file_in}; // Reads strings of wchar_t
std::istream_iterator<std::wstring, wchar_t> end_in; // End-of-stream iterator
```

第一条语句定义了一个 wchar_t 字符的输入文件流。为了读这个文件，第二条语句定义了一个流迭代器。流中的字符类型是由 istream_iteratot 的第二个模板类型参数指定的，在这个实例中是 wchar_t。当然，指定从流中读入的对象的类型的第一个模板类型参数必须是 wstring，它是 wchar_t 字符的字符串类型。

istream_iterator 对象有下面这些成员函数：

*   opemtor*() 会返回一个流中当前对象的引用，可以运用这个运算符多次以重读相同的值。
*   operator->() 会返回流中当前对象的地址。
*   apemtor++() 会从底层输入流中读取一个值，并将它保存到一个迭代器对象中，返回一个迭代器对象的引用。因此，表达式 *++in 的值为最新的被保存的值。这不是一般的用法，因为它可能会跳过流中的第一个值。
*   operator++(int) 会从底层输入流读取一个值，并将它保存到一个迭代器对象中，为使用 operator*() 或 operator->() 访问做准备。在流中的新值被保存之前，这个函数会返回迭代器对象的一个代理。这意味着在读和保存底层输入流中的最新值之前，表达式 *in++ 的值是保存在迭代器中的对象。

也有非成员函数，operator==() 和 operator!=() 可以比较相同类型的对象。两个输入迭代器是相等的，前提是它们都是同一个流的迭代器或者都是流的结束迭代器；否则，它们就不相等。

## 迭代器和流迭代器

输入流迭代器和常规迭代器在数据项序列的关联方式上是不同的，明白这一点很重要。常规迭代器指向的是数组或容器中的元素。递增一个常规迭代器会改变它所指向的元素；这对指向同一个序列的元素的其他迭代器没有影响。这一点和流迭代器不同。

当用流迭代器来从标准输入流读取数据时，显然要考虑会发生什么；当流迭代器指向文件时，就可能不那么明显，但仍然可以应用。如果生成了两个和同一个流相关的输入流迭代器，初始时它们都指向第一个数据项。如果用一个迭代器来读取流，另一个不会再引用第一个数据值。当从标准输入流读取时，值会被第一个迭代器消耗。这是因为这个迭代器在读取一个值时，会修改流对象。输入流迭代器不仅会改变它所指向的元素(在引用时得到的结果)，也会改变底层流中确定下一次读操作从哪里开始的位置。

因此，给定流的两个或两个以上的输入流迭代器会指向从这个流中得到的下一个数据项。这意味着两个输入流迭代器所确定的序列只能由开始迭代器和流的结束迭代器组成。我们无法生成两个指向同一个流中两个不同值的流迭代器，这并不是说不能用输入流迭代器来访问数据项。在适当时，我们会看到可以这么做。

## 用输入流的成员函数读取数据

下面是一些说明如何用成员函数读取字符串的代码：

```
std::cout << "Enter one or more words. Enter ! to end:";
std::istream_iterator<string> in {std::cin}; // Reads strings from cin
std::vector<string> words;
while(true)
{
    string word = *in;
    if(word == "!"") break;
    words.push_back(word);
    ++in;
}
std::cout << "You entered " << words.size() << "words." << std::endl;
```

循环从标准输入流中读取单词，并把它们添加到 vector 容器中，直到按下回车键。表达式 *in 的值是从底层流读到的当前 string 对象。++in 会导致从流中读取下一个 string 对象，并保存到这个迭代器中。下面是执行这段代码的示例输出：

Enter one or more words. Enter ! to end:
Yes No Maybe !
You entered 3 words.

下面是一个演示如何用成员函数来读取数值数据的示例，但不一定要这样使用：

```
// Calling istream_iterator function members
#include <iostream>                                   // For standard streams
#include <iterator>                                   // For stream iterators

int main()
{
    std::cout << "Enter some integers - enter Ctrl+Z to end.\n";
    std::istream_iterator<int> iter {std::cin};         // Create begin input stream iterator...
    std::istream_iterator<int> copy_iter {iter};        // ...and a copy
    std::istream_iterator<int> end_iter;                // Create end input stream iterator

    // Read some integers to sum
    int sum {};
    while(iter != end_iter)                             // Continue until Ctrl+Z read
    {
        sum += *iter++;
    }
    std::cout << "Total is " << sum << std::endl;

    std::cin.clear();                                   // Clear EOF state
    std::cin.ignore();                                  // Skip characters

    // Read integers using the copy of the iterator
    std::cout << "Enter some more integers - enter Ctrl+Z to end.\n";
    int product {1};
    while(true)
    {
        if(copy_iter == end_iter) break;                  // Break if Ctrl+Z was read
        product *= *copy_iter++;
    }
    std::cout << "product is " << product << std::endl;
}
```

在显示输入提示后，我们可以用一个输入流迭代器从 cin 中读取 int 类型的值；然后会复制迭代器对象的一个副本。在原始对象 iter 被使用后，我们可以用副本 y_iter 从 cin 读取输入，我们只需要一个结束迭代器对象，因为它从没有改变。第一个循环会求出所有用输入流迭代器读入的值的和，直到识别出 EOF 状态，它是通过从流中读取 Ctrl+Z 标志设置的。解引用 iter 可以使它所指向的值变得可用，之后，后递增运算会移动 iter，使它指向下一个输入。如果输入的是 Ctrl+Z，循环结束。

在从 cin 读取更多数据之前，必须调用流对象的 dear() 来重置 EOF 标志；也需要跳过输入缓冲区中留下的 '\n' 字符，这是通过调用流对象的 ignore 做到的。第二个循环会用 copy_iter 来读取值并计算它们的 product。第一个循环的主要差别是:它是通过比较 copy_iter 和 end_iter 的相等性来终止循环的。

下面是这个示例的输出：

Enter some integers - enter Ctrl+Z to end.
1 2 3 4^Z
Total is 10
Enter some more integers - enter Ctrl+Z to end.
3 3 2 5 4^Z
product is 360

在大多数时候，我们并不会像这样使用输入流迭代器。一般来说，只需要用一个流的开始和结束迭代器作为一个函数的参数。我们可能意识到，能够只用一条语句来替代上述程序中的第一个循环及其后面的输出语句：

```
std::cout << "Total is " << std::accumulate(iter, end_iter, 0) <<std::endl;
```

下面是通过用一个输入流迭代器，将从 cin 得到的浮点值插入到一个容器中的代码：

```
std::vector<double> data;
std::cout <<"Enter some numerical values — enter Ctrl+Z to end.\n";
std::copy(std::istream_iterator<double>{std::cin}, std::istream iterator <double>{}, std::back_inserter(data));
```

copy() 算法会将任意个数的值附加到 vector 容器中，直到读到 Ctrl+Z。vector 容器有一个接受序列来初始化元素的构造函数，因此可以用生成容器的语句中的输入流迭代器来读取值：

```
std::cout << "Enter some numerical values - enter Ctrl+Z to end.\n";
std::vector<double> data {std::istream_iterator<double>{std::cin},std::istream_iterator<double>{}};
```

这会从标准输入流中读取浮点值，并用它们作为容器中元素的初始值。