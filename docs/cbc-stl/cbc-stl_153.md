# C++ partial_sum(STL partial_sum)部分和计算方法详解

定义在 header 头文件中的 partial_sum() 可以计算输入序列中元素的部分和，并将结果保存到一个输出序列中。

partial_sum() 会计算出输入序列中长度从 1 开始不断增加的序列的和，所以第一个输出值就是第一个元素，下一个值是前两个元素的和，再下一个值就是前三个元素的和，以此类推。这和 adjacent_difference() 算法是相反的，因此 partial_sum() 不会做 adjacent_difference() 做的事。下面是一个示例：

```
std::vector<int> data {2, 3, 5, 7, 11, 13, 17, 19};
std::cout << "Partial sums: ";
std::partial_sum(std::begin(data), std::end(data),std::ostream_iterator<int>{std::cout, " "});
std::cout << std::endl; // Partial sums: 2 5 10 17 28 41 58 77
```

可以看到，输出是由长度稳定增加的序列的和组成的。通过执行下面的代码，可以很容易展示出这些结果：

```
std::vector<int> data {2, 3, 5, 7, 11, 13, 17, 19};
std::cout << "Original data: ";
std::copy(std::begin(data), std::end(data), std::ostream_ iterator<int>{std::cout, " "});
std::adjacent_difference(std::begin(data), std::end(data),std::begin (data));
std::cout << "\nDifferences : ";
std::copy(std::begin(data), std::end(data),std::ostream_iterator<int>{std::cout," "});
std::cout << "\nPartial sums : ";
std::partial_sum(std::begin(data), std::end(data),std::ostream_iterator<int>{std::cout, " "});
std::cout << std::endl;
```

注意，这里的输出迭代器和输入序列的开始迭代器是相同的。这是合法的，可以认为 data 是可覆盖的，但算法被定义为阻止这么做。执行这段代码后得到的输出如下：

Original data: 2 3 5 7 11 13 17 19
Differences: 2 1 2 2 4 2 4 2
Partial sums: 2 3 5 7 11 13 17 19

输出显示了用原始值的差值计算出的部分和，这一点不令人吃惊。像 adjacent_difference() 算法一样，可以提供一个函数对象作为 partial_sum() 的额外参数，额外参数被用来定义一个代替加法的运算符。下面是它可能的运用方式：

```
std::vector<int> data {2, 3, 5, 7, 11, 13, 17, 19};
std::cout << "Partial sums:";
std::partial_sum (std::begin (data) , std::end (data),std::ostream_iterator<int>{std::cout, " "}, std::minus<int>());
std::cout << std::endl;  // Partial sums: 2 -1 -6 -13 -24 -37 -54 -73
```

这里使用了减法运算符，所以值是 2、2-3、2-3-5、2-3-5-7 的结果，以此类推。