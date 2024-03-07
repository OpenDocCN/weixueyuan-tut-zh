# C++ unordered_map 删除元素（erase 删除元素）详解

可以调用 unordered_map 的成员函数 erase() 来移除元素。参数可以是标识元素的一个键或是指向它的一个迭代器。当参数是键时，erase() 会返回一个整数，它是移除元素的个数，所以 0 表示没有找到匹配的元素。当参数是迭代器时，返回的迭代器指向被移除元素后的元素。下面是一些示例：

```
auto n = people.erase ("Jim");// Returns 0 if key not found
auto iter = people.find ("May") ; // Returns end iterator if key not found
if(iter != people.end())
    iter = people.erase (iter) ;// Returns iterator for element after "May"
```

也可以移除指定的一个元素序列。例如：

```
//Remove all except 1st and last
auto iter = people.erase(++std:rbegin(people),--std:rend(people));
```

返回的迭代器指向被移除的最后一个元素的下一个位置。

成员函数 clear() 会移除所有的元素。当容器中没有元素时，成员函数 empty() 返回 true。