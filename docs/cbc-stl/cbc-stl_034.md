# C++ list(STL list)删除元素方法详解

对于 list 的成员函数 clear() 和 erase()，它们的工作方式及效果，和前面的序列容器相同。list 容器的成员函数 remove() 则移除和参数匹配的元素。例如：

```
std::list<int> numbers { 2, 5, 2, 3, 6, 7, 8, 2, 9};
numbers.remove(2); // List is now 5 3 6 7 8 9
```

第二条语句移除了 numbers 中出现的所有值等于 2 的元素。

成员函数 remove_if() 期望传入一个一元断言作为参数。一元断言接受一个和元素同类型的参数或引用，返回一个布尔值。断言返回 true 的所有元素都会被移除。例如：

```
numbers.remove_if([](int n){return n%2 == 0;});// Remove even numbers. Result 5 3 7 9
```

这里的参数是一个 lambda 表达式，但也可以是一个函数对象。

成员函数 unique() 非常有意思，它可以移除连续的重复元素，只留下其中的第一个。例如：

```
std::list<std::string> words { "one", "two", "two", "two","three", "four", "four"};
words.unique () ; // Now contains "one" "two" "three" "four"
```

这个版本的 unique() 函数使用 == 运算符比较连续元素。可以在对元素进行排序后，再使用 unique()，这样可以保证移除序列中全部的重复元素。

重载的 unique() 版本接受一个二元断言作为参数。使断言返回 true 的元素被认为是相等的。这提供了一个非常灵活的相等概念。然后就可以将两个长度或初始字符相同的字符串看作相等。这个断言可以有不同类型的参数，只要 list 迭代器解引用后的结果可以隐式地相互进行类型转换。