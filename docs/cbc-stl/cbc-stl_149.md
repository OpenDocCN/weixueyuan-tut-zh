# C++ iota 函数用法详解

定义在 numeric 头文件中的 iota() 函数模板会用连续的 T 类型值填充序列。前两个参数是定义序列的正向迭代器，第三个参数是初始的 T 值。第三个指定的值会被保存到序列的第一个元素中。保存在第一个元素后的值是通过对前面的值运用自增运算符得到的。当然，这意味着 T 类型必须支持 operator++()。下面展示了如何生成一个有连续的浮点值元素的 vector 容器：

```
std::vector<double> data(9);
double initial {-4};
std::iota (std::begin (data) , std::end (data) , initial);
std::copy(std::begin(data), std::end(data),std::ostream_iterator<double>{std::cout<< std::fixed << std::setprecision(1), " "});
std::cout << std::endl;  // -4.0 -3.0 -2.0 -1.0 0.0 1.0 2.0 3.0 4.0
```

以 4 为初始值调用 iota() 会将 data 中元素的值设为从 -4 到 +4 的连续值。当然，初始值并不需要一定是整数：

```
std::iota(std::begin(data), std::end(data), -2.5);
// Values are -2.5 -1.5 -0.5 0.5 1.5 2.5 3.5 4.5 5.5
```

增量是 1，因此 data 中的值和注释显示的一样。可以将 iota() 算法应用到任意类型的序列上，只要它有自增运算符。下面是另一个示例：

```
string text {"This is text"};
std::iota(std::begin(text), std::end(text), 'K');
std::cout << text << std::endl;   // Outputs: KLMNOPQRSTUV
```

很容易看到输出如注释所示，字符串中的字符被设为以 K 开头的字符序列。这个示例发生了什么并不是那么明显：

```
std::vector<string> words (8);
std::iota(std::begin(words), std::end(words), "mysterious");
std::copy(std::begin(words), std::end(words),std::ostream_iterator<string>{std::cout, " "});
std::cout << std::endl; // mysterious ysterious sterious terious erious rious ious ous
```

输出如注释所示。这是该算法的一个有趣应用，但没有什么用处。这只适用于第三个参数是一个字符串常量的情形。如果参数是 string{"mysterious"}，将无法通过编译，因为没有为 string 类定义 operator++()。字符串常量对应的值是一个 const char* 类型的指针，可以将 ++ 运算符应用到它上面。因此对于 words 中第一个元素后的每个元素，指针的递增会导致字符串常量前面的字母被丢弃。将 ++ 应用到指针的结果是生成一个 string 对象，然后它会被保存到当前的元素序列中。只要 ++ 可以应用到序列中的元素类型上，就能将 iota() 算法应用到序列上。

注意：很有趣的是，iota() 算法来源于 IBM 的编程语言 APL 中的 iota 运算符 ι。在 APL 中，表达式 ι[10] 会生成从 1 到 10 的整数的 vector。APL 是肯•艾弗森在 20 世纪 60 年代发明的。它是一门相当筒洁的语言，能够隐式处理 vector 和数组。APL 的一个完整程序会从键盘读取任意个值，计算出它们的平均值，然后输出被表示为 10 个字符结果。