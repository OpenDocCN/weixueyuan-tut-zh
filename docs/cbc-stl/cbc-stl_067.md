# C++ set 添加、删除和访问(STL set 添加、删除和访问)元素详解

set 中没有实现成员函数 at()，也没有实现 operator[]() 。除了这些操作外，set 容器提供 map 容器所提供的大部分操作。可以使用 insert()、emplace()、emplace_hint() 成员函数来向 set 中添加元素。

## 添加元素

下面是一个使用 insert() 的示例：

```
std::set<string, std::greater<string>> words {"one", "two", "three"};
auto pr1 = words.insert("four");
auto pr2 = words.insert ("two") ;
auto iter3 = words.insert(pr.first, "seven");
words.insert ({ "five","six"}) ;
string wrds[] {"eight", "nine", "ten"};
words.insert(std::begin(wrds) , std::end(wrds));
```

插入单个元素会返回一个 pair<iterator，bool> 对象。插入单个元素和一个标识，会返回一个迭代器。插入一段元素或一个初始化列表就不会有返回值。当 insert() 的参数是初始化列表时，会用列表中的字符串创建 string 对象。下面是两个在 set 容器中创建元素的示例：

```
std::set<std::pair<string,string>> names;
auto pr = names.emplace("Lisa", "Carr");
auto iter = names.emplace_hint(pr.first, "Joe", "King");
```

这和 map 一样。成员函数 emplace() 会返回一个 pair<iterator,bool> 对象，而 emplace_hint() 只返回一个迭代器。前者的参数被直接传入元素的构造函数，用来创建元素。emplace_hint() 的第一个参数是一个迭代器，它指出了元素可能的插入位置，随后的参数会被传入元素的构造函数。

## 删除元素

成员函数 clear() 会删除 set 的所有元素。成员函数 erase() 会删除迭代器指定位置的元素或与对象匹配的元素。例如：

```
std::set<int> numbers {2, 4, 6, 8, 10, 12, 14};
auto iter = numbers.erase(++std::begin(numbers));
auto n = numbers.erase(12);
n = numbers.erase(13);
numbers.clear();
```

成员函数 erase() 可以删除一段元素：

```
std::set<int> numbers {2, 4, 6, 8, 10, 12, 14};
auto iter1 = std::begin(numbers); // iter1 points to 1st element
advance(iterl, 5); // Points to 6th element-12
auto iter = numbers.erase(++std:rbegin(numbers), iter1);// Remove 2nd to 5th inclusive. iter points to 12
```

如果 set 没有元素，成员函数 empty() 返回 true，成员函数 size() 返回它所包含的元素个数。如果担心无法在 set 中存储尽可能多的元素，可以调用成员函数 max_size() 来得到可存储的最大元素个数，这显然会是一个很大的值。

## 访问元素

set 的成员函数 find() 会返回一个和参数匹配的元素的迭代器。如果对象不在 set 中，会返回一个结束迭代器。例如：

```
std::set<string> words {"one", "two","three", "four","five"};
auto iter = words.find ("one") ; // iter points to "one"
iter = words.find(string{"two"});   // iter points to "two"
iter = words.find ("six");   // iter is std:: end (words)
```

调用成员函数 count() 可以返回指定键所对应的元素个数，返回值通常是 0 或 1，因为 set 容器中的元素是唯一的。set 容器模板定义了成员函数 equal_range()、lower_bound()、 upper_bound()，这和 multiset 容器在很大程度上是一致的。