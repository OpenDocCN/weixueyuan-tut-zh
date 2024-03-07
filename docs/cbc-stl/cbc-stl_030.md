# C++ deque 添加和删除元素方法详解

deque 和 vector 都有成员函数 push_back() 和 pop_back()，它们在序列尾部添加或删除元素的方式相同。deque 也有成员函数 push_front() 和 pop_front()，可以在序列头部执行相似的操作。例如：

```
std::deque<int> numbers {2, 3, 4};
numbers.push_front(11); // numbers contains 11 2 3 4
numbers.push_back(12);  // numbers contains 11 2 3 4 12
numbers.pop_front();    // numbers contains 2 3 4 12
```

除了和 vector —样都有 emplace_back() 函数外，deque 还有成员函数 emplace_front()，可以在序列的开始位置生成新的元素。和 vector 一样，也可以使用 emplace() 或 insert() 在 deque 内部添加或移除元素。这个过程相对要慢一些，因为这些操作需要移动现有的元素。

本教程所描述的关于 vector 容器的所有 insert() 函数也同样适用于 deque 容器。在 deque 的任意位置插入一个元素会让现有的迭代器全部失效，因此需要重新生成它们。deque 的成员函数 erase() 也和 vector 的相同，它的成员函数 clear() 可以移除一些元素。