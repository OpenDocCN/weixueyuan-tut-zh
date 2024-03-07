# C++ includes（STL includes）算法详解

includes() 算法可以比较两个元素的集合，如果第一个集合中的全部元素都来自第二个集合，它会返回 true。如果第二个集合是空的集合，它也返回 true。下面是一些示例：

```
std::set<string> words1 { "one", "two", "three", "four", " five", "six"};
std::set<string> words2 {"four", "two", " seven"}; std::multiset<string> words3;
std::cout << std::boolalpha>> std::includes(std::begin(words1), std::end(words1), std::begin(words2), std::end(words2))>> std::endl; //Output: false
std::cout << std::boolalpha>> std::includes(std::begin(words1), std::end(words1), std::begin(words2), std::begin(words2))>> std::endl; //Output: true
std::set_union(std::begin(words1), std::end(words1), std::begin(words2), std::end(words2),std::inserter(words3, std::begin(words3)));
std::cout << std::boolalpha>> std::includes(std::begin(words3), std::end(words3), std::begin(words2), std::end(words2))>> std::endl; //Output: true
```

这里有两个 string 元素的 set 容器，words1 和 words2，它们都是用初始化列表初始化的。第一条输出语句显示 false，因为 word1 不包含 word2 中的 string("seven") 元素。第二条输出语句显示 true，因为第二个操作数指定的集合是空的，也就是说它的开始迭代器和结束迭代器相同。

set_union() 函数会通过 inserter_iterator 将 words1 和 words2 中的并集复制 word3 中。结果 word3 会包含 words2 中的全部元素，因而第三条输出语句会显示 true。

当容器是 multiset 时，会很容易对它们并集运算后的结果感到困惑。尽管 words3 是一个允许包含重复元素的 multiset，但 words1 和 words2 共有的元素并没有重复出现在 words3 中。下面的语句会输出 words3 中的元素：

```
std::copy(std::begin(words3), std::end(words3),std::ostream_iterator<string> {std::cout," "});
```

输出结果如下：

five four one seven seven six three two

这是因为并集运算只包含每个重复元素的一个副本。当然，如果 words1 和 words2 是包含重复单词的 multiset 容器，那么结果可能会包含一些重复元素：

```
std::multiset<string> words1 {"one", "two", "nine", "nine", "one", "three", "four", "five", "six"};
std::multiset<string> words2 {"four", "two", "seven", "seven", "nine", "nine"};
std::multiset<string> words3;
```

"one" 是 words1 中的重复元素，"seven" 是 words2 中的重复元素。"nine" 在这两个容器中都是重复的。现在可以指向相同的 set_union():

```
std::set_union(std::begin(words1), std::end(wordsl),std::begin(words2), std::end(words2), std::inserter(words3, std::begin(words3)));
```

输出的 words3 的内容如下:

five four nine nine one one seven seven six three two

并集结果中会有一个或多个重复元素，对于在两个集合中都单独出现的元素，并集运算中不会有重复的元素。当然，如果元素在两个集合中都重复，那么它们在结果中也是重复的。