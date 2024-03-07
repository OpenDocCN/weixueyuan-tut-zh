# C++ find_if（STL find_if）查找算法详解

find_if() 同 find() 一样，为在输入迭代器所定义的范围内查找单个对象的算法，它可以在前两个参数指定的范围内查找可以使第三个参数指定的谓词返回 true 的第一个对象。谓词不能修改传给它的对象。

find_if() 会返回一个指向被找到对象的迭代器，如果没有找到对象，会返回这个 序列的结束迭代器。

可以按如下方式使用 find_if() 来查找 numbers 中第一个大于 value 的元素：

```
int value {5};
auto iter1 = std::find_if(std::begin(numbers), std::end(numbers),value { return n > value; });

if(iter1 != std::end(numbers))
    std::cout << *iter1 << " was found greater than " << value << ".\n";
```

find_if() 的第三个参数是一个 lambda 表达式的谓词。这个 lambda 表达式以值的方式捕获 value，并在 lambda 参数大于 value 时返回 true。这段代码会找到一个值为 46 的元素。