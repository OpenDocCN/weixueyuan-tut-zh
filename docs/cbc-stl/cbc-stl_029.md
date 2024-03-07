# C++ deque 获取（访问）元素方法详解

可以使用下标运算符来访问 deque 容器中的元素。这个操作和 vector 容器中的类似，所以下标也没有做边界检查。

deque 容器中的元素是序列，但是内部的存储方式和 vector 不同。它组织元素的方式导致容器的大小总是和容量相等。因为这个，所以没有定义成员函数 capacity()，deque 只有成员函数 size()，它以成员类型 size_type 的无符号整型来返回当前元素个数。

同样因为 deque 内部组织元素的方式不同，deque 的操作和 vector 相比要慢。

可以用下标运算符来访问元素，但是索引并没有进行边界检查。为了用进行边界检查的索引来访问元素，可以选择使用成员函数 at()，这和 vector 相同：

```
std::cout << words.at(2) << std::endl; // Output the third element in words
```

参数必须是一个 size_t 类型的值，因此不能是负数。如果 at() 的参数不在范围内，例如大于 words.size()-1，就会抛出一个 std::out_of_range 异常。

deque 成员函数 front() 和 back() 的用法也和 vector 相同，然而，deque 却没有成员函数 data()，因为元素并没有被存放在数组中。deque 容器和 vector 一样，有三种不同重载版本的 resize() 函数，它们的操作基本相同。