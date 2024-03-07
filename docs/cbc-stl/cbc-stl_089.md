# C++ find_if_not（STL find_if_not）查找算法详解

find_if_not() 可以在输入迭代器所定义的范围内查找单个对象，该算法会在前两个参数指定的范围内查找可以使第三个参数指定的谓词 返回 false 的第一个对象。谓词不能修改传给它的对象。

find_if_not() 会返回一个指向被找到对象的迭代器，如果没有找到对象，会返回这个序列的结束迭代器。可以按如下方式用 find_if_not() 算法来查找使谓词为 false 的元素：

```
size_t count {}; int five {5};
auto start_iter = std::begin(numbers);
auto end_iter = std::end(numbers);
while((start_iter = std::find_if_not(start_iter, end_iter,five {return n > five; })) != end_iter)
{
    ++count;
    ++start_iter;
}
std::cout << count << " elements were found that are not greater than "<< five << std::endl;
```

find_if_not() 的第三个参数是一个谓词，它和先前在 find_if() 算法中使用的 lambda 表达式相似。当元素大于 five 时，这个函数才会返回 true。当谓词返回 false 时，就找到了一个元素，所以这个操作可以用来查找小于或等于 five 的元素。这段代码会找到 5 个元素，它们分别是 5、-5、-6、5、5。