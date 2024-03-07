# C++ vector 迭代器及用法

正如期望的那样，vector 容器实现了所有可以返回迭代器的成员函数，包括 const 迭代器和 non-const 迭代器，以及反向迭代器。

vector 容器的迭代器是随机访问迭代器。当然，也可以通过全局函数获取它们。vector 有成员函数 push_back()，所以能够通过使用 back_insert_iterator 来添加新的值。

从前面章节了解到，可以通过调用全同的 back_inserter() 函数来获取一个后向插入迭代器。无法在 vector 奔器中使用 front_insert_iterator，这需要 vector 有成员函数 push_front()，但是 vector 容器并没有定义这个函数。

可以通过演示如何用 copy() 算法来添加元素，向你展示怎样在 vector 中使用后向插入迭代器。copy() 的头两个参数是两个迭代器，指定了复制元素的范围，第三个参数指定了这些元素存放的位置。头两个参数要求是输入迭代器，所以可以接受任何其他类别的迭代器；显然第三个参数必须是一个输出迭代器。这里有一个示例：

```
std::vector<double> data {32.5, 30.1, 36.3, 40.0, 39.2};
std::cout << "Enter additional data values separated by spaces or Ctrl+Z to end:" << std::endl;

std::copy(std::istream_iterator<double>(std::cin) , std::istream_iterator<double>(),std::back_inserter(data));
std::copy(std::begin(data), std::end(data),std::ostream_iterator<double> (std:: cout," "))
```

用初始化列表生成 data 容器。第一次调用 copy() 时，使用一个 istream_iterator 对象作为第一个参数，它能够从标准输入流中读取 double 类型的值。第二个参数是一个流的结束迭代器，当识别到流结束时，istream_iterator 会变为结束迭代器；当从键盘输入 `Ctrl+Z` 时, 这也会发生在 cin 中。

copy() 的第三个参数是读入值的存放位置，是 data 容器的一个 back_insert_iterator，它是由 back_inserter() 函数返回的，因此从 cin 读出的值都被作为新元素添加到 data 容器的后面。最后一次调用 copy()，会将 data 容器的所有元素复制到 cout；这是通过将一个 ostream_iterator 对象作为目的地址来实现的。让我们使用 vector 容器的迭代器来尝试一个完整的示例：

```
#include <iostream>
#include <string>
#include <algorithm>
#include <vector>
using std::string;
using std::vector;

int main()
{
    vector<string> words;                     // Stores words to be sorted
    words.reserve(10);                        // Allocate some space for elements
    std::cout << "Enter words separated by spaces. Enter Ctrl+Z on a separate line to end:" << std::endl;

    std::copy(std::istream_iterator <string> {std::cin}, std::istream_iterator <string> {},std::back_inserter(words));

    std::cout << "Starting sort." << std::endl;
    bool out_of_order {false};
    while (true)
    {
        for (auto first = start + 1; first != last; ++first)
        {
            if (*(first - 1) > *first)
            { // Out of order so swap them
                std::swap(*first, *(first - 1));
                out_of_order = true;
            }
        }
        if (!out_of_order)                      // If they are in order (no swaps necessary)...
            break;                                // ...we are done...
        out_of_order = false;                   // ...otherwise, go round again.
    }

    std::cout << "your words in ascending sequence:" << std::endl;
    std::copy(std::begin(words), std::end(words), std::ostream_iterator < string > {std::cout, " "});
    std::cout << std::endl;

    // Create a new vector by moving elements from words vector
    vector<string> words_copy {std::make_move_iterator(std::begin(words)),std::make_move_iterator(std::end(words))};
    std::cout << "\nAfter moving elements from words, words_copy contains:" << std::endl;
    std::copy(std::begin(words_copy), std::end(words_copy),  std::ostream_iterator < string > {std::cout, " "});
    std::cout << std::endl;

    // See what's happened to elements in words vector...
    std::cout << "\nwords vector has " << words.size() << " elements\n";
    if (words.front().empty())
    std::cout << "First element is empty string object." << std::endl;

    std::cout << "First element is \"" << words.front() << "\"" << std::endl;
}
```

示例输出如下：

Enter words separated by spaces. Enter Ctrl+Z on a separate line to end:
one two three four five six seven eight
^Z

Starting sort.
your words in ascending sequence:
eight five four one seven six three two

After moving elements from words, words_copy contains:
eight five four one seven six three two

words vector has 8 elements
First element is empty string object.
First element is ""

该程序使用流迭代器从标准输入流读取单词，然后将其作为字符串对象写入一个 vector 容器。可以输入任意个数的单词。容器会在必要时自动增长。这里调用容器的 reserve()  函数来为 10 个元素分配内存。一个好主意是，每次只分配大致需要的内存，这会减少小幅度分配内存所带来的开销。

back_inserter() 生成了一个 back_insert_iterator，它能够调用容器的成员函数 push_back()，来将每一个字符串对象作为新元素添加到容器中。

copy() 算法的头两个参数是输入流迭代器，其中的第二个参数是结束流迭代器。当从键盘输入 `Ctrl+Z` 时，流迭代器就会匹配到它，这相当于文件流的 EOF。

这里对 vector 元素进行排序的代码展示了迭代器的使用。稍后你会看到，sort() 算法可以只用一条语句就完成相同的工作。这里的排序算法是十分简单的冒泡排序，通过遍历元素来反复排序。在每一趟排序中，如果临近的元素无序，就会互相交换。swap() 函数定义在 <algorithm.h> 头文件中，可以高效地交换任何类型的元素。如果在一趟排序中，所有元素都没有交换，那么所有元素已经是升序序列了。最外层的循环是一个由迭代器控制的 for 循环。first 的初始值是 begin(words)+1，它指向 vector 的第二个元素。从第二个元素开始， 是为了确保能够使用 first-1,这样可以保证两个连续元素的比较总是合法。当 first 自增到 end(words) 时，一趟排序就会结束。

对 words vector 中的内容排序后的结果，可以通过使用 copy() 算法将全部元素转移到输出流迭代器来显示。转移元素的范围是由 begin() 和 end() 返回的迭代器指定的，所以会输出全部元素。ostream_iterator 构造函数的参数是数据流向的目的地址，分隔字符串会分隔每一个输出的值。

main() 的最后一部分代码展示了如何使用移动迭代器，这里移动了所有元素。在这个操作之后，从程序输出可以发现，words 中包含的字符串都变成了空字符串。移动一个元素会留下一个由无参字符串构造函数创建的对象。一般来说，移动一个是类对象的元素，会导致这个元素处于一种不确定的状态，因此我们不应该再去使用这个对象。

main() 中的排序代码其实并不依赖存放元素的容器。它只要求迭代器指定的元素能够支持排序算法的运算。STL 有一个 sort() 函数模板，它远比我们能想出的任何方法都好。有时候，我们也可以定义自己的函数模板，去对能够排序的任意类型元素进行排序：

```
template<typename RandomIter>
void bubble_sort(RandomIter start, RandomIter last)
{
    std::cout << "Starting sort." << std::endl;
    bool out_of_order {false};                // true when values are not in order
    while (true)
    {
        for (auto first = start + 1; first != last; ++first)
        {
            if (*(first - 1) > *first)
            { // Out of order so swap them
                std::swap(*first, *(first - 1));
                out_of_order = true;
            }
        }
        if (!out_of_order)                      // If they are in order (no swaps necessary)...
            break;                                // ...we are done...
        out_of_order = false;                   // ...otherwise, go round again.
    }
}
```

模板类型参数是迭代器类型。因为 for 循环中迭代器算术操作的原因，bubble_sort() 算法需要使用随机访问迭代器。只要容器可以提供随机访问迭代器，算法就可以对这个容器的内容进行排序；这也包括标准数组和字符串对象。如果在前面的 main() 中使用此代码， 就可以使用下面的语句替换掉 main() 中对 words 进行排序的部分：

```
bubble_sort(std::begin(words), std::end(words)); // Sort the words array
```

定义一个只用迭代器实现操作的函数模板，会使这个函数的用法变得更灵活。任何处理一段元素的算法都可以用这种方式生成。