# c++ set_difference(STL set_difference)算法详解

set_difference() 算法可以创建两个集合的差集，它也有两个版本的函数并且参数集和 set_union() 相同。下面是一个对降序 set 容器使用这个算法的示例：

```
std::set<string, std::greater<string>> words1 {"one", "two", "three", "four", "five", "six" };
std::set<string, std::greater<string>> words2 { "four", "five", "six", "seven", "eight", "nine"};
std::set<string, std::greater<string>> result;
std::set_difference(std::begin(words1) , std::end(words1),
std::begin(words2), std::end(words2), std::inserter(result, std::begin(result)),std::greater<string>());
// Result: "two", "three", "one"
```

这里调用的这个版本的函数的第 6 个参数是一个用来比较元素的函数对象，因为来自 set 容器的这段元素也用这个函数排序。通过从 words 集合中移除 word1 和 word2 共有的元素来获取差集，差集由来自 word1 的元素组成。结果得到 words1 的前三个元素的降序序列。这个算法也会返回一个迭代器，它指向目的容器被插入的最后一个元素的下一个位置。

## set_symmetric_difference()算法

set_symmetric_difference() 算法和先前的集合算法遵循同一种模式。下面的几条语句展示了它的用法：

```
std::set<string> words1 { "one", "two", "three", "four", "five", "six" };
std::set<string> words2 {"four", "five", "six", "seven", "eight", "nine"};
std::set_symmetric_difference(std::begin(words1), std::end(words1),std::begin(words2), std::end(words2),std::ostream_iterator<string> {std::cout," "});
```

这个范围内的元素默认是升序排列的。集合的对称差集中的元素是两个集合中不包括它们共有元素的元素。最后一个函数的参数定义了结果集的存放位置，它是一个 ostream_iterator，因此这些元素会被写入 cout，输出内容如下：

eight nine one seven three two

自然地，这些序列中的 string 元素都是对它们使用 < 运算符后得到的结果，因为默认的比较函数对象类型是 less<string>。