# 流缓冲区迭代器（输入流缓冲区迭代器和输出流缓冲区迭代器）详解

流缓冲区迭代器不同于流迭代器，流迭代器只会传送字符到流缓冲区或从流缓冲区读出字符。它们可以直接访问流的缓冲区，因此不包含插入和提取运算符。也没有数据的转换，数据之间也不需要分隔符，即使有分隔符，也可以自己处理它们。因为它们读写数据时没有数据转换，流缓冲区迭代器适用于二进制文件。流缓冲区迭代器读写字符的速度比流迭代器快。

istreambuf_iterator 模板定义了输入迭代器，ostreambuf_iterator 模板定义了输出迭代器。可以构造读写任意 char、 wchar_t、 char 16_t、char32 类型的字符的流缓冲区迭代器。

## 输入流缓冲区迭代器

为了生成可以从流中读取任意给定类型的字符输入流迭代器，需要将一个流对象传给构造函数：

```
std::istreambuf_iterator<char> in {std::cin};
```

这个对象是一个可以从标准输入流读取任意 char 类型字符的输入流缓冲区迭代器。默认构造函数生成的对象表示的是流结束迭代器：

```
std::istreambuf_iterator<char> end_in;
```

可以用这两个迭代器从 cin 中读取字符序列到字符串中，直到在单行中输入的 Ctrl+Z 被作为流结束的信号。例如：

```
std::cout << "Enter something: ";
string rubbish {in, end_in};
std::cout << rubbish << std::endl; // Whatever you enter will be output
```

用从键盘输入的全部字符来初始化 string 对象 rubbish，直到遇到流的结束符。输入流缓冲区迭代器有下面这些成员函数：

*   operator*()：返回的是流中当前字符的副本。流的位置不会被提前，因此可以反复地获取当前字符。
*   operator->()：可以访问当前字符的成员，如果它有成员的话。
*   operator++() 和 operator++(int)：都会将流的位置移到下一个字符。operator++() 在移动位置后才返回流迭代器，operatar++(int) 在被移动位置之前返回流迭代器的一个代理。前缀 ++ 运算符很少使用。
*   equal() 接受另一个输入流缓冲区迭代器为参数，并返回 true，前提是当前迭代器和参数都不是流结束迭代器，或者都是流结束迭代器。如果它们之中只有一个流结束迭代器，会返回 false。

也有非成员函数，operator==() 和 opemrator!=()，可以用来比较两个迭代器。我们不得不依靠流的结束来终止输入。可以用递增和解引用运算符来从流中读取字符，直到找到特定的字符。例如：

```
std::istreambuf_iterator<char> in {std::cin};
std::istreambuf_iterator<char> end_in;
char end_ch { '*' };
string rubbish;
while(in != end_in && *in != end_ch) rubbish += *in++;
std::cout << rubbish << std::endl; // Whatever you entered up to ' * ' or EOF
```

while 循环会从 cin 中读取字符，直到识别到流的结束符或者在回车后输入一个星号。循环体中会运用解引用运算符来返回流中的当前字符，然后，后缀自增运算符会移动迭代器，使它指向下一个字符。注意循环表达式中的解引用说明它没有改变迭代器；只要不是 '*'，在迭代器递增之前，循环体中的相同字符会被再次读取。

## 输出流缓冲区迭代器

通过传给构造函数一个流对象，可以生成一个 ostreambuf_iterator 对象来将给定类型的字符写到流中：

```
string file_name {"G:/Beginning_STL/junk.txt"};
std::ofstream junk_out {file_name};
std::ostreambuf_iterator<char> out {junk_out};
```

输出对象可以将 char 类型的字符写到文件输出流 junk_out 中，junk_out 封装了名为 junk.txt 的文件。为了读取不同类型的字符，例如 char32_t，需要指定模板类型参数作为字符类型。当然，必须为字符类型生成流，因此不能用 ofstream，因为 ofstream 是 basic_ofstream<char> 类型的别名。下面是一个示例：

```
string file_name { "G:/Beginning_STL/words.txt"}; std::basic_ofstream<char32_t> words_out {file_name};
std::ostreambuf_iterator<char32_t> out {words_out};
```

这个流缓冲区迭代器可以将 Unicode 字符写到流缓冲区。有 wchar_t 类型的字符的文件流被定义为别名 wofstream。

也可以通过将一个流缓冲区的地址传给构造函数来生成输出流缓冲区对象。可以这样来生成上面的 out 对象：

```
std::ostreambuf_iterator<char> out {junk_out.rdbuf()};
```

ofstream 对象的成员函数 rdbuf() 会返回流内部的缓冲区的地址。成员函数 rdbuf() 继承自 ios_base，它是所有流对象的基类。

ostreambuf_iterator 对象有下面这些成员函数：

*   operator=()：会将参数字符写到流缓冲区中。如果识别到 EOF，就说明流缓冲区是满的，这个写操作失败。
*   当上一次写缓冲区失败时，failed() 返回 true。当识别到 EOF 时，会发生这种情况，因为输出流缓冲区是满的。
*   operator*()：不做任何事。之所以定义它，是因为它需要的 ostreambuf_iterator 对象是一个输出迭代器。
*   operator++() 和 operator++(int)：不做任何事。之所以定义它们，是因为它们需要的 ostreambuf_iterator 对象是一个输出迭代器。

通常只关心成员函数赋值运算符。下面是使用它的一种方式：

```
string ad {"Now is the discount of our winter tents! \n"};
std::ostreambuf_iterator<char> iter {std::cout}; // Iterator for output to cout
for(auto ch: ad)
    iter = ch;  // Write the character to the stream
```

执行这段代码会将字符串逐字节写到标准输出流中。当然，通过调用 copy() 算法也可以得到同样的结果：

```
std::copy(std::begin(ad), std::end(ad), std::ostreambuf_iterator<char>{std::cout});
```

这两个示例中的编码方式有些滑稽可笑，因为它们等同于下面这行代码：

```
std::cout << ad;
```

尽管它并没有告诉我们关于输出流缓冲区迭代器的更多信息。