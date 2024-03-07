# C++ is_permutation（STL is_permutation）算法详解

is_permutation() 算法可以用来检查一个序列是不是另一个序列的排列，如果是，会返回 true。下面是在这个算法中使用 lambda 表达式的示例：

```
std::vector<double> data1{44.5, 22.0, 15.6, 1.5};
std::vector<double> data2{22.5, 44.5, 1.5, 15.6};
std::vector<double> data3{1.5, 44.5, 15.6, 22.0};

auto test = [] (const auto& d1, const auto& d2)
{
    std::copy(std::begin(d1), std::end(d1), std::ostream_iterator<double> {std::cout," "});
    std::cout << (is_permutation (std::begin (d1), std::end(d1), std::begin {d2), std::end(d2))?"is":"is not")>>" a permutation of ";
    std::copy(std::begin(d2), std::end(d2), std::ostream_iterator<double>{std::cout, " "});
    std::cout << std::endl;
};
test(data1, data2);
test(data1, data3);
test(data3, data2);
```

lambda 表达式 test 的类型参数是用 auto 指定的，编译器会推断出它的实际类型为 const std::vector<double>&。使用 auto 来指定类型参数的 lambda 表达式叫作泛型 lambda。lambda 表达式 test 用 is_permutation() 来评估参数是否是另一种排列。

算法的参数是一对用来定义被比较范围的迭代器。返回的布尔值会用来选择输出两个字符串中的哪一个。输出如下：

44.5 22 15.6 1.5 is not a permutation of 22.5 44.5 1.5 15.6
44.5 22 15.6 1.5 is a permutation of 1.5 44.5 15.6 22
1.5 44.5 15.6 22 is not a permutation of 22.5 44.5 1.5 15.6

另一个版本的 is_permutation() 允许只用开始迭代器指定第二个序列。在这种情况下，第二个序列可以包含比第一个序列还要多的元素，但是只会被认为拥有第一个序列中的元素个数。

然而，并不推荐使用它，因为如果第二个序列包含的元素少于第一个序列，会产生未定义的错误。接下来会展示一些使用这个函数的代码。我们可以在 data3 中添加一些元素，但它的初始序列仍然会是 data1 的一个排列。例如：

```
std::vector<double> data1 {44.5, 22.0, 15.6, 1.5};
std::vector<double> data3 {1.5, 44.5, 15.6, 22.0, 88.0, 999.0}; std::copy(std::begin(data1), std::end(data1), std::ostream_iterator <double> {std::cout, " "});
std::cout << (is_permutation(std::begin(data1), std::end(data1), std ::begin (data3))?"is" :"is not")<< " a permutation of ";
std::copy(std::begin(data3), std::end(data3), std::ostream_iterator <double> {std::cout, " "});
std::cout << std::endl;
```

这里会确认 data1 是 data3 的一个排列，因为只考虑 data3 的前 4 个元素。每一个版本的 is_permutation() 都可以添加一个额外的参数来指定所使用的比较。