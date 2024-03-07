# C++ set 迭代器（STL set 迭代器）详解

set<T> 容器的成员返回的迭代器都是双向迭代器。这些迭代器的类型的别名定义在 set<T> 模板中，可以从 set 中得到类型别名有 iterator、reverse_iterator、const_iterator、 const_reverse_iterator，从它们的名称就可以看出它们的类型。例如:

*   成员函数 begin() 和 end() 会返回 iterator 类型的迭代器;
*   成员函数 rbegin() 和 rend() 会返回 reverse_iterator 类型的迭代器;
*   成员函数 cbegin() 和 cend() 会返回 const_iterator 类型的迭代器。
*   成员函数 crbegin() 和 crend() 可以返回 const_reverse_iterator 类型的迭代器。

然而，set 容器的迭代器类型的别名有时会让人产生一些误解。所有 set<T> 容器的成员函数返回的迭代器都指向 const T 类型的元素。因此，iterator 迭代器会指向 const 元素， reverse_iterator 和其他类型的迭代器也是如此。这意味着我们不能修改元素。如果想要修改 set 容器中的元素，必须先删除它，然后再插入修改后的版本。

仔细思考一下，其实这是不合理的。set 中的对象以它们自己作为键，对象在容器中的位置是通过比较对象决定的。如果可以修改元素，元素的顺序就失效了，也会扰乱后面的访问操作。、

当必须修改元素而且仍然需要将它们组合到一个或多个 set 容器中时，还有一个方法可以做到这一点。可以在 set 容器中保存指针，并且最好选择智能指针。当使用 set 容器时，通常会在它们中保存 shared_ptr<T> 或 weak_ptr<T> 对象。

在 set 容器中保存 unique_ptr<T> 对象没有多少意义。因为容器中不存在和 unique_ptr<T> 对象匹配的独立键，所以我们从来不会直接检索元素。