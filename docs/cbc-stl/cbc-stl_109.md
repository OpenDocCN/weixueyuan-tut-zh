# C++ copy_n(STL copy_n)算法详解

copy_n() 算法可以从源容器复制指定个数的元素到目的容器中。第一个参数是指向第一个源元素的输入迭代器，第二个参数是需要复制的元素的个数，第三个参数是指向目的容器的第一个位置的迭代器。这个算法会返回一个指向最后一个被复制元素的后一个位置的迭代器，或者只是第三个参数——输出迭代器——如果第二个参数为 0。下面是一个使用它的示例：

```
std::vector<string> names {"A1","Beth", "Carol", "Dan", "Eve","Fred","George" ,"Harry", "Iain", "Joe"};
std::unordered_set<string> more_names {"Janet", "John"};
std::copy_n(std:rbegin(names)+1, 3, std::inserter(more_names, std::begin(more_names)));
```

这个 copy_n() 操作会从 names 的第二个元素开始复制 3 个元素到关联容器 more_names 中。目的容器是由一个 unordered_set 容器的 insert_iterator 对象指定的，它是由 inserter() 函数模板生成的。insert_iterator 对象会调用容器的成员函数 insert() 来向容器中添加元素。

当然，copy_n() 的目的地址也可是以流迭代器：

```
std::copy_n(std::begin(more_names), more_names.size()-1,std::ostream_iterator<string> {std::cout, " "});
```

这样会输出 more_names 中除了最后一个元素之外的全部元素。注意，如果被复制元素的个数超过了实际元素的个数，程序会因此崩溃。如果元素的个数为 0 或负数，copy_n() 算法什么也不做。