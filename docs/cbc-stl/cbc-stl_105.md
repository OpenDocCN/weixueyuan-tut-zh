# C++（STL） lexicographical_compare 字符串排序算法详解

两个字符串的字母排序是通过从第一个字符开始比较对应字符得到的。第一对不同的对应字符决定了哪个字符串排在首位。字符串的顺序就是不同字符的顺序。如果字符串的长度相同，而且所有的字符都相等，那么这些字符串就相等。如果字符串的长度不同，短字符串的字符序列和长字符串的初始序列是相同的，那么短字符串小于长字符串。因此 “age” 在“beauty” 之前，“a lull” 在 “a storm” 之前。显然，“the chicken” 而不是 “the egg” 会排在首位。

对于任何类型的对象序列来说，字典序都是字母排序思想的泛化。从两个序列的第一个元素开始依次比较对应的元素，前两个对象的不同会决定序列的顺序。显然，序列中的对象必须是可比较的。

lexicographical_compare()算法可以比较由开始和结束迭代器定义的两个序列。它的前两个参数定义了第一个序列，第 3 和第 4 个参数分别是第二个序列的开始和结束迭代器。默认用 < 运算符来比较元素，但在需要时，也可以提供一个实现小于比较的函数对象作为可选的第 5 个参数。如果第一个序列的字典序小于第二个，这个算法会返回 true，否则返回 false。所以，返回 false 表明第一个序列大于或等于第二个序列。

序列是逐个元素比较的。第一对不同的对应元素决定了序列的顺序。如果序列的长度不同，而且短序列和长序列的初始元素序列匹配，那么短序列小于长序列。长度相同而且对应元素都相等的两个序列是相等的。空序列总是小于非空序列。下面是一个使用 lexicographical_compare() 的示例：

```
std::vector<string> phrase1 {"the", "tigers", "of", "wrath"};
std::vector<string> phrase2 {"the", "horses", "of", "instruction"};
auto less = std::lexicographical_compare (std::begin (phrase1), std: :end (phrase1),
std::begin(phrase2), std::end(phrase2)); std::copy(std::begin(phrase1), std::end(phrase1), std::ostream_iterator<string>{std::cout, " "});
std::cout << (less ? "are":"are not") << " less than ";
std::copy(std::begin(phrase2), std::end(phrase2), std::ostream_iterator <string>{std::cout, " "});
std::cout << std::endl;
```

因为这些序列的第二个元素不同，而且“tigers”大于“horses”，这段代码会生成如下 输出：

the tigers of wrath are not less than the horses of instruction

可以在 lexicographical_compare() 调用中添加一个参数，得到相反的结果：

```
auto less = std::lexicographical_compare (std::begin (phrase1), std::end (phrase1),std::begin(phrase2), std::end(phrase2), [](const string& s1, const string& s2) { return s1.length() < s2.length(); });
```

这个算法会使用作为第 3 个参数的 lambda 表达式来比较元素。这里会比较序列中字符串的长度，因为 phrase1 中第 4 个元素的长度小于 phrase2 中对应的元素，所以 phrase1 小于 phrase2。