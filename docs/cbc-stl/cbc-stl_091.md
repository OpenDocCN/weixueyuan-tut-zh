# C++ adjacent_find(STL adjacent_find)算法使用详解

adjacent_find() 算法可以用来搜索序列中两个连续相等的元素。用 `==` 运算符来比较连续的一对元素，返回的迭代器指向前两个相等元素中的第一个。如果没有一对相等的元素，这个算法返回这个序列的结束迭代器。例如：

```
string saying {"Children should be seen and not heard."};
auto iter = std::adjacent_find(std::begin(saying), std::end(saying));
if (iter != std::end(saying))
    std::cout <<"In the following text: \n\""<< saying << "\"\n'"<< *iter << "' is repeated starting at index position "<< std::distance(std::begin(saying), iter) << std::endl;
```

这里会搜索 saying 字符串的前两个相等字符，所以这段代码的输出如下：

In the following text:
"Children should be seen and not heard."
'e' is repeated starting at index position 20

adjacent_find() 算法的第二个版本，允许我们提供一个应用于连续元素的谓词。下面展示了如何用这个函数来查找这个序列中第一对都为奇数的连续整数：

```
std::vector<long> numbers {64L, 46L, -65L, -128L, 121L, l7L, 35L, 9L, 91L, 5L};
auto iter = std::adjacent_find(std::begin(numbers),std::end(numbers),[](long n1, long n2){ return n1 % 2 && n2 % 2; });
if(iter != std::end(numbers))
    std::cout <<"The first pair of odd numbers is "<< *iter << " and " << *(iter+1) << std::endl;
```

当两个参数都不能被 2 整除时，这个 lambda 表达式就返回 true，所以这段代码会找到 121 和 17。