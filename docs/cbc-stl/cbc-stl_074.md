# C++ unordered_set 查找元素（find 查找元素）详解

调用 unordered_set 的 find() 会返回一个迭代器。这个迭代器指向和参数哈希值匹配的元素，如果没有匹配的元素，会返回这个容器的结束迭代器。例如：

```
std::pair<string, string> person {"John", "Smith"};
if(names.find(person) != std::end(names))
    std::cout << "We found " << person.first << " " << person.second << std::endl;
else
    std::cout << "There's no " << person.first << " " << person.second << std::endl;
```

这个容器是从上一节获取的，这段代码会报告 John Smith 是存在的。如果不存在，find() 会返回这个容器的结束迭代器，并且会执行第二条输出语句。

unordered_set 容器中的元素是无序的，因此也不需要成员函数 upper_bound() 和 lower_bound()。成员函数 equal_range() 会返回一个以迭代器为成员的 pair，它指定了和参数匹配的一段元素。

unordered_set 容器中只可能有一个匹配元素。如果没有，两个迭代器都是容器的结束迭代器。调用成员函数 count() 会返回容器中参数的出现次数。对于 unordered_set 只可能是 0 或 1。当想要知道容器中总共有多少元素时，可以调用成员函数 size()。如果容器中没有元素，成员函数 empty() 会返回 true。