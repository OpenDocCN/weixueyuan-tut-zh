# C++ set_intersection(STL set_intersection)用法详解

除了会创建两个集合的交集而不是并集之外，set_intersection() 算法的用法和 set_union() 相同。有两个版本的 set_intersection()，它们和 set_union() 拥有相同的参数集。下面的一些语句可以说明它的用法：

```
std::set<string> words1 {"one", "two", "three", "four", "five", "six"};
std::set<string> words2 {"four"，"five", "six", "seven", "eight", "nine"};
std::set<string> result;
std::set_intersection(std::begin(words1), std::end(words1), std::begin(words2), std::end(words2),std::inserter(result, std::begin(result)));
// Result: "five" "four" "six"
```

这个 set 容器保存 string 对象，默认使用 less<string> 的实例对元素排序。两个容器中元素的交集是它们共有的元素，它们被保存在 result 容器中。当然，这些元素是升序字符串序列。set_intersection() 算法会返回一个迭代器，它指向目的容器中插入的最后一个元素的下一个位置。