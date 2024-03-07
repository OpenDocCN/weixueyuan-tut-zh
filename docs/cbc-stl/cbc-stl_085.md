# C++ is_sorted(STL is_sorted)排序算法详解

排序是要耗费时间的，尤其是在有大量元素时。测试元素段是否已经排好序可以避免不必要的排序操作。如果两个迭代器参数所指定范围内的元素是升序排列的，函数模板 is_sorted() 就会返回 true。

为了能够顺序处理元素，迭代器至少需要是正向迭代器。提醒一下，正向迭代器支持前缀和后缀形式的自增运算。下面是一个使用 is_sorted() 的示例：

```
std::vector<int> numbers {22, 7, 93, 45, 19};
std::vector<double> data {1.5, 2.5, 3.5, 4.5};
std::cout << "numbers is "<<(std::is_sorted(std::begin (numbers), std::end(numbers)) ? "": "not ") << "in ascending sequence.\n";
std::cout << "data is "<< (std::is_sorted(std::begin(data), std::end(data)) ?"":"not ") << "in ascending sequence." << std::endl;
```

默认使用的比较函数是 < 运算符。鉴于“data is”的输出，表明 numbers 不是一个升序序列。还有一个版本也可以指定用于比较元素的函数对象：

```
std:: cout << "data reversed is "<< (std::is_sorted(std::rbegin(data), std::rend(data), std::greater<>()) ? "":"not ")<< "in descending sequence." << std::endl;
```

这条语句的输出说明 data 中元素的逆序是降序。

也可用函数模板 is_sorted_until() 来判断一个元素段是否有序。它的参数用来定义要测试的迭代器。这个函数会返回一个指向这段元素中升序序列上边界元素的迭代器。例如：

```
std::vector<string> pets {"cat", "chicken", "dog", "pig", "llama", "coati", "goat"};
std::cout << "The pets in ascending sequence are:\n";
std::copy(std::begin(pets), std::is_sorted_until(std::begin(pets), std::end (pets)) , std::ostream_iterator<string>{ std::cout," "});
```

copy() 算法的前两个参数分别是 pets 容器的开始迭代器以及当 is_sorted_until() 应用到 pets 中全部元素上时返回的迭代器。is_sorted_until() 算法会返回指向 pets 中升序序列元素的上边界，它是指向小于其前面元素的第一个元素的迭代器。如果序列是有序的，则返回一个结束迭代器。这段代码的输出如下：

The pets in ascending sequence are:
cat chicken dog pig

"llama"是小于其前者的第一个元素，所以”pig”就是升序序列的第一个元素。

可以选择提供一个用于比较元素的函数对象：

```
std::vector<string> pets {"dog", "coati", "cat", "chicken", "pig", "llama", "goat"};
std::cout << "The pets in descending sequence are:\n";
std::copy(std::begin(pets),std::is_sorted_until(std::begin(pets), std::end (pets) , std::greater<>()),std::ostream_iterator<string>{ std::cout," "});
```

这一次我们会查找降序元素，因为使用的是 string 类的成员函数 operator>() 来比较元素。输出为：

The pets in descending sequence are:
dog coati cat

"chicken" 是大于其前者的第一个元素，所以 is_sorted_until() 返回的迭代器就指向这个元素，因而 "cat" 是降序序列的最后一个元素。