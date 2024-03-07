# C++ map 的创建（STL map 的创建）详解

map 类模板有 4 个类型参数，但一般只需要指定前两个模板参数的值。第 1 个是键的类型，第 2 个是所保存对象的类型，第 3 个和第 4 个模板参数分别定义了用来比较键的函数对象的类型以及为 map 分配内存的对象的类型。最后两个参数有默认值。在本节稍后部分会展示如何定义不同类型的比较键的函数对象，但不会定义可替代的分配器类型。

map<> 容器类的默认构造函数会创建一个空的 map 容器。例如，可以创建一个这样的容器，size_t 类型的值表示年龄，作为它保存的值，string 类型的值表示名称，作为它的键：

```
std::map<std::string, size_t> people;
```

第 1 个模板类型参数指定键的类型是字符串，第 2 个模板类型参数指定值的类型为 size_t。当然，这里的模板类型参数可以是任何类型，唯一的要求是键必须可以用 less<K> 比或用自己指定的另一个函数对象来替代。

map<K，T> 中的每个元素都是同时封装了对象及其键的 pair<const K，T> 类型对象，这里不能修改 const K。pair<T1,T2> 类的模板定义在 utility 头文件中，它被包含在 map 头文件中。因此 people 容器中的元素是 pair<const string,size_t> 类型的。pair<T1，T2> 这种模板类型并不是专门在这种情况下使用的。必要时可以用它将两个不同类型的对象组装成一个对象。本章稍后将讲解更多这方面的内容。

我们可以用初始化列表来指定 map 的初始值，但因为 map 中包含的是 pair<const K，T> 类型的元素，所以初始化列表中的值也必须是这种类型。下面展示了如何为 people 容器设置初始值：

```
std::map<std::string, size_t> people{{"Ann", 25}, {"Bill", 46},{"Jack", 32},{"Jill", 32}};
```

初始化列表中的值是通过将每个嵌套花括号中的两个值传递给构造函数产生的，因此列表会包含 4 个 pair<const string,size_t> 对象。

utility 头文件中定义了 make_pair < T1，T2 >() 函数模板，它提供了一种组合 T1 和 T2 类型对象的简单方法。因此，可以按如下方式创建 pair 对象来初始化 map:

```
std::map<std::string,size_t> people{std::make_pair("Ann",25),std::make_pair("Bill", 46),std::make_pair("Jack", 32),std::make_pair("Jill", 32)};
```

make_pair<T1，T2>() 函数模板从函数参数中推断出类型参数值，因而由参数列表中调用 make_pair<>() 返回的是 <char const*,int> 类型的对象。因为这些对象都是 map 容器 people 的初始值，所以这些 pair 对象会被转换成 map 中元素的类型，即 pair<const string，size_t>。

pair<T1, T2> 的公共成员变量 first 和 second 分别保存了存储 T1 和 T2 类型的对象。只要原始 pair 对象的成员变量 first 和 second 可以隐式转换为与目标 pair 对象成员变量相同类型的变量，pair<T1;T2> 模板的构造函数就可以提供这种类型的隐式转换。

map<K,T> 模板定义了移动和复制构造函数，所以可以复制现有的容器。例如：

```
std::map<std::string, size_t> personnel {people}; // Duplicate people map
```

map 容器 personnel 包含 people 元素的副本。

可以用另一个容器的一段元素来创建一个 map，用开始和结束迭代器以通常的方式指定元素。显然，迭代器指向的 pair 元素的类型必须和容器兼容。这里有一个示例：

```
std::map<std::string, size_t> personnel {std::begin(people),std::end(people)};
```

这样就生成了 personnel，并且用 people 容器的迭代器指定的元素对它进行了初始化。map 容器提供了双向迭代器，这样就可以通过自增或自减访问元素。map 容器还提供了反向迭代器，所以可以从最后一个元素遍历到第一个元素。personnel 容器包含的元素和 people 完全相同。当然，也可以用另一个容器的元素子集来创建容器：

```
std::map<std::string,size_t> personnel {++std::begin(people),std::end(people)};
```