# C++输出流迭代器（STL 输出流迭代器）详解

输出流迭代器是由 ostream_iterator 模板定义的，这个模板的第一个模板参数是被写值的类型，第二个模板参数是流字符的类型；第二个模板参数默认是 char 类型的值。ostream_iterator 是一个能够将任意 T 类型对象写到文本模式的输出流中的输出迭代器；只要 T 类型的对象实现了将 T 类型对象写到流中的 operator<<()。

因为它是一个输出迭代器，所以它支持前向和后向递增操作，并且是一个单向传入的迭代器。输出流迭代器定义了它的拷贝赋值运算符，因此可以用插入运算符将 T 对象写到流中。默认的输出流迭代器会按照 char 字符的序列来写入值。通过指定第二个模板类型参数，可以写包含不同类型字符的流。

ostream_iterator 类型定义了下面这些成员函数：

*   构造函数：第一个构造函数会用作为第一个参数的 ostream 对象的输出流生成一个开始迭代器，第二个参数是分隔符字符串。输出流对象会在每个被它写入到流中的对象的后面写分隔符字符串。第二个构造函数可以省略第二个差数，它会生成一个写对象时后面不跟分隔符的迭代器。
*   operator=(const T& obj) 会将 obj 写到流中，然后写分隔符字符串，前提是在构造函数中指定了一个。这个函数返回一个迭代器的引用。
*   operator*() 不做任何事，除了返回迭代器对象。为了将迭代器限定为输出迭代器，这个操作必须被定义。
*   operator++() 和 operator++(int) 都被定义了但不做任何事，除了返回迭代器对象。为了将迭代器限定为输出迭代器，前向或后向自增操作必须被支持。

不做任何事的运算符函数是必要的，因为它们是说明输出迭代器可以做些什么的规范的一部分。如果想写数据到一个文本模式的流中，并且随后打算以文本方式读取，流的值之间就需要分隔符。出于这个原因，尽管可以显式地写分隔符，但通常会使用有两个参数的构造函数。

## 输出流迭代器成员函数的使用

下面是一个示例，展示了成员函数可以被使用的各种方式：

```
// Using output stream iterator function members
#include <iostream>                                      // For standard streams
#include <iterator>                                      // For iterators and begin() and end()
#include <vector>                                        // For vector container
#include <algorithm>                                     // For copy() algorithm
#include <string>
using std::string;

int main()
{
    std::vector<string> words {"The", "quick", "brown", "fox", "jumped", "over", "the", "lazy", "dog"};

    // Write the words container using conventional iterator notation
    std::ostream_iterator<string> out_iter1 {std::cout};   // Iterator with no delimiter output
    for(const auto& word : words)
    {
        *out_iter1++ = word;                                 // Write a word
        *out_iter1++ = " ";                                  // Write a delimiter
    }
    *out_iter1++ = "\n";                                   // Write newline

    // Write the words container again using the iterator
    for(const auto& word : words)
    {
        (out_iter1 = word) = " ";                            // Write the word and delimiter
    }
    out_iter1 = "\n";                                      // Write newline

    // Write the words container using copy()
    std::ostream_iterator<string> out_iter2 {std::cout, " "};
    std::copy(std::begin(words), std::end(words), out_iter2);
    out_iter2 = "\n";
}
```

这段代码以 3 种方式将 words 容器中的元素写到标准输出流中。out_iter1 流迭代器通过调用只以输出流为参数的构造函数而生成。第一个循环以常规方式输出迭代器符号，在解引用后递增它，并复制 word 的当前值到 out_iter1 的解引用的结果中。循环后面的语句会在流中写一个新行。注意，不能这样写：

```
out_iter1 = '\n';   // Won't compile!
```

定义的迭代器会将 string 对象写入流中，因此它不能写任何其他类型的数据。成员函数 operator=() 只接受一个 string 参数，因此这条语句无法通过编译。

像前面描述的那样，opemtor*() 和前自增及后自增运算符除了返回迭代器的引用外，不会做任何事。因此可以去除这些操作，像第二个循环中的语句那样，不使用它们也可以产生同样的输出。为了保证应用分隔符的第二条赋值语句有输出迭代器作为它的左操作数，语句中的括号是必要的。

第三行的输出是通过以你在前面的章节中看到过的方式调用 copy() 算法生成的。元素 的值会被复制到 out_iter2 中，定义它的构造函数的第二个参数指定了每个输出值后面的分隔符字符串。