# C++ map（STL map）删除元素（erase 函数删除元素）详解

map 的成员函数 erase() 可以移除键和参数匹配的元素，然后返回所移除元素的个数，例如：

```
std::map<std::string, size_t> people {{ "Fred", 45}, {"Joan", 33},{"Jill", 22}};
std::string name{"Joan"};
if(people.erase(name))
    std::cout << name << " was removed." << std::endl;
else
    std::cout << name << " was not found" << std::endl;
```

显然，map 容器的返回值只可能是 0 或 1，0 表明元素不在容器中。也可以用指向删除元素的迭代器作为 erase() 的参数。这种情况下，返回的迭代器指向被删除元素的下一个位置。这个参数必须是容器中的有效迭代器，不能是结束迭代器。如果迭代器参数指向的是容器的最后一个元素，那么会返回结束迭代器。例如：

```
auto iter = people.erase(std::begin(people));
if(iter == std::end(people))
    std::cout << "The last element was removed."<< std::endl;
else
    std::cout << "The element preceding " << iter->first << "was removed." << std::endl;
```

当最后一个元素被移除时，这段代码会输出一条消息或被移除元素后面元素的键。也有更高级版本的 erase()，它可以移除两个迭代器参数所定义范围内的元素，例如：

```
auto iter = people.erase(++std::begin(people), --std::end(people));//Erase all except 1st & last
```

返回的迭代器指向这段元素中最后一个被删除的元素。如果想删除容器中的所有元素，可以调用成员函数 clear()。