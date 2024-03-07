# C++ adjacent_difference 相邻差计算方法详解

numeric 头文件中的 adjacent_difference() 算法可以算出输入序列中相邻元素对的差，并将它们保存到另一个序列中。第一个元素会被原封不动地复制到新的序列中，然后用第二个元素减去第一个元素的结果作为新序列的第二个元素，再用第三个元素减去第二个元素的结果作为新序列的第三个元素，以此类推。例如：

```
std::vector<int> data {2, 3, 5, 7, 11, 13, 17, 19}; std::cout << "Differences: ";
std::adjacent_difference(std::begin(data), std::end(data),std::ostream_iterator<int>{std::cout," "});
std::cout << std::endl;// Differences: 2 1 2 2 4 2 4 2
```

data 容器中元素之间的差值被 adjacent_difference() 算法直接输出，因为输出序列的迭代器是一个写到 cout 的输出流迭代器。这里产生的输出如注释所示。

这个算法的第二个版本允许指定一个运算符来代替应用到元素对上的减法运算符。下面是一个示例：

```
std::vector<int> data {2, 3, 5, 7, 11, 13, 17, 19};
std::cout << "Products : ";
std::adjacent_difference (std::begin (data), std::end(data),std::ostream_iterator<int>{std::cout, " "},std::multiplies<>());
std::cout << std::endl;  // Products : 2 6 15 35 77 143 221 323
```

第 4 个参数是一个指定元素之间运算的函数对象，在这个示例中是一个来自于 functional 头文件的 multiplies 实例。可以看到，这样会得到 data 中连续元素的乘积。这个函数可以接受任何二元运算，只要不改变输入序列或者使迭代器无效。下面是一个将 plus<T> 函数对象用作元素对之间的运算符来计算 Fibonacci 数列的示例：

```
std::vector<size_t> fib(15, 1); // 15 elements initialized with 1
std::adjacent_difference(std::begin(fib), std::end(fib)-1, std::begin(fib)+1,std::plus<size_t>());
std::copy(std::begin(fib), std::end(fib),std::ostream_iterator<size_t>{std::cout," "});
std::cout << std::endl; // Output: 1 1 2 3 5 8 13 21 34 55 89 144 233 377 610
```

这里 adjacent_diflference() 算法会对 fib 容器中的元素对进行相加，并将结果写回从第二个元素开始的 fib 容器。fib 的最后一个元素没有包含输入序列，输入序列中最后两个元素的和会覆盖最后一个元素的值。在这个运算之后，fib 会包含一个从 1 开始的 Fibonacci 数列。注释显示了 copy() 算法生成的输出。