# C++ min_element、max_element 和 minmax_element 求极值算法详解

algorithm 头文件中定义了 3 个可以运用到序列的算法：

*   min_element()：会返回一个指向输入序列的最小元素的迭代器；
*   max_element()：会返回指向最大元素的迭代器；
*   minmax_element()：会以 pair 对象的形式返回这两个迭代器。

序列必须由正向迭代器指定，仅仅有输入迭代器是不够的。对于这 3 个算法，除了序列的开始和结束迭代器，可以选择提供第三个参数来定义比较函数。下面是将这 3 个算法应用到整数的一些代码：

```
std::vector<int> data {2, 12, 3, 5, 17, -11, 113, 117, 19}; std::cout << "From values ";
std::copy(std::begin(data), std::end(data),std::ostream_iterator<int>{std::cout, " "});
std::cout << "\n Min = " << *std::min_element(std::begin(data), std::end (data))<< " Max = " << *std::max_element (std::begin (data), std::end(data))<< std::endl;
auto start_iter = std::begin(data) + 2;
auto end_iter = std::end(data) - 2;
auto pr = std::minmax_element(start_iter, end_iter); // Get min and max
std::cout << "From values ";
std::copy(start_iter, end_iter, std::ostream_iterator<int>{std::cout, " "});
std::cout << "\n Min = "<< *pr.first << " Max = " << *pr.second << std::endl;
```

min_element() 和 max_element() 被用来从 data 中查找最小值和最大值。minmax_element() 会被应用到同一个序列上，但会略过前两个和后两个元素。执行这段代码会输出下面的内容：

From values 2 12 3 5 17 -11 113 117 19
Min = -11 Max = 117
From values 3 5 17 -11 113
Min = -11 Max = 113

algorithm 头文件中也定义了 min()、max()、minmax() 的函数模板，它们分别返回最小值、最大值或者两个对象或一个对象的初始化列表的最小值和最大值。我们已经看到它们可以用来比较两个参数。下面是一个将它们用于初始化列表的示例：

```
auto words = {string {"one"}, string {"two"}, string {"three"}, string {"four"}, string {"five"}, string {"six"}, string {"seven"}, string {"eight"}};
std::cout << "Min = " << std::min(words)<< std::endl;//Min = eight
auto pr = std::minmax(words, [] (const strings s1, const strings s2){return s1.back() < s2.back();});
std::cout << "Min = " << pr.first << " Max = " << pr.second<< std::endl; // Min = one Max = six
```

words 是 string 对象的一个初始化列表。元素都是 string 对象很重要。如果使用简单的 char*，那么算法将无法正常工作，因为比较的是地址而不是 string 的内容。min() 算法会用 string 对象默认的 operator<() 来确定 words 中的最小对象，然后用 minmax() 找出列表中最小和最大对象，minmax() 使用一个自定义的比较字符串的最后一个字符的比较函数。结果显示在注释中。也有接受函数对象作为定义比较的最后一个参数的 min() 和 max() 版本。