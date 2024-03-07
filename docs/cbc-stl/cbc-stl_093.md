# C++ search(STL search)算法详解

在查找序列的子序列方面，search() 算法和 find_end() 算法相似，但它所查找的是第一个匹配项而不是最后一个。

和 find_end() 算法一样，它也有两个版本，第二个版本接受用来比较元素的谓词作为第 5 个参数。可以用 search() 来验证前面使用 find_end() 搜索的结果。 如何改变每次遍历搜索的具体范围是它们的主要不同之处。下面是一个示例：

```
string text {"Smith, where Jones had had \"had\", had had \"had had\"."" \"Had had\" had had the examiners\' approval."};
std::cout << text << std::endl;
string phrase {"had had"};
size_t count {};
auto iter = std::begin(text);
auto end_iter = end(text);
while((iter = std::search(iter, end_iter, std::begin(phrase), std::end (phrase) , [](char ch1, char ch2) { return std::toupper (ch1) == std:: toupper (ch2); })) != end_iter)
{
    ++count;
    std::advance(iter, phrase.size()); // Move to beyond end of subsequence found
}
std::cout << "\n\""<< phrase << "\" was found "<< count << " times." << std::endl;
```

这段代码执行后会输出下面的内容：

Smith, where Jones had had "had", had had "had had". "Had had" had had the examiners' approval.
"had had" was found 5 times.

我们仍然忽略大小写来搜索“had had”，但会正向查找第一个匹配项。search() 算法返回的迭代器指向找到的子序列的第一个元素，因此为了搜索 phrase 的第二个实例，iter 必须增加 phrase 中元素的个数，使它指向下一个找到的序列的第一个元素。