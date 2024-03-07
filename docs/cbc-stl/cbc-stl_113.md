# C++ unique(STL unique)算法详解

unique() 算法可以在序列中原地移除重复的元素，这就要求被处理的序列必须是正向迭代器所指定的。在移除重复元素后，它会返回一个正向迭代器作为新序列的结束迭代器。可以提供一个函数对象作为可选的第三个参数，这个参数会定义一个用来代替 `==` 比较元素的方法。例如：

```
std::vector<string> words {"one", "two", "two", "three", "two", "two", "two"};
auto end_iter = std::unique(std::begin(words), std::end(words));
std::copy(std::begin(words), end_iter, std::ostream_iterator<string>{std::cout, " "});
std::cout << std::endl;
```

这样会通过覆盖来消除 words 中的连续元素。输出为：

one two three two

当然，没有元素会从输入序列中移除;算法并没有方法去移除元素，因为它并不知道它们的具体上下文。整个序列仍然存在。但是，无法保证新末尾之后的元素的状态；如果在上面的代码中用 std::end(words) 代替 end_iter 来输出结果，得到的输出如下:

one two three two two two

相同个数的元素仍然存在，但新的结束迭代器指向的元素为空字符串；最后两个元素还和之前一样。在你的系统上，可能会有不同的结果。因为这个，在执行 unique() 后，最好按如下方式截断序列：

```
auto end_iter = std::unique(std::begin(words), std::end(words));
words.erase(end_iter, std::end(words));
std::copy (std::begin (words) , std::end (words) , std::ostream iterator<string> {std::cout, " "});
std::cout << std::endl;
```

容器的成员函数 erase() 会移除新的结束迭代器之后的所有元素，因此 end(words) 会返回 end_iter。

当然，可以将 unique() 运用到字符串中的字符上：

```
string text {"There's no air in spaaaaaace!"};
text.erase(std::unique(std::begin(text), std::end(text),[](char ch1, char ch2) { return ch1 = ' '&& ch1 = ch2; }), std::end(text));
std::cout << text << std::endl; // Outputs: There's no air in spaaaaaace!
```

这里使用 unique() 会移除字符串 text 中的连续重复的空格。这段代码会用 unique() 返回的迭代器作为 text 成员函数 erase() 的第一个参数，而且它会指向被移除的第一个字符。erase() 的第二个参数是 text 的结束迭代器，因此在没有重复元素的新字符串之后的所有字符都会被移除。