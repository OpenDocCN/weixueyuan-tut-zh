# C++ rotate_copy（STL rotate_copy）算法详解

rotate_copy() 算法会在新序列中生成一个序列的旋转副本，并保持原序列不变。rotate_copy() 的前 3 个参数和 copy() 是相同的；第 4 个参数是一个输出迭代器，它指向目的序列的第一个元素。这个算法会返回一个目的序列的输出迭代器，它指向最后一个被复制元素的下一个位置。例如：

```
std::vector<string> words {"one", "two", "three", "four", "five","six", "seven", "eight", "nine"，"ten"};
auto start = std::find(std::begin(words), std::end(words), "two");
auto end_iter = std::find (std::begin(words) , std::end (words) ,"eight");
std::vector<string> words_copy;
std::rotate_copy(start, std::find(std::begin(words), std::end(words),"five") , end_iter, std::back_inserter (words_copy));
std::copy(std::begin(words_copy), std::end(words_copy),std::ostream_iterator<string> {std::cout, " "});
std::cout << std::endl;
```

这段代码会对 word 中从 "two" 到 "seven" 的元素生成一个旋转副本。通过使用 back_insert_iterator 将复制的元素追加到 words_copy 容器中，back_insert_iterator 会调用 words_copy 容器的成员函数 push_back() 来插入每个元素。这段代码产生的输出如下：

five six seven two three four

这里 rotate_copy() 返回的迭代器是 words_copy 中元素的结束迭代器。在这段代码中，并没有保存和使用它，但它却很有用。例如：

```
std::vector<string> words {"one”，"two", "three", "four", "five","six", "seven", "eight", "nine", "ten"};
auto start = std::find (std::begin(words) , std::end(words) ,"two");
auto end_iter = std::find(std::begin(words) , std::end(words),"eight"); std::vector<string> words_copy {20}; // vector with 20 default elements
auto end_copy_iter = std::rotate_copy(start,std::find(std::begin(words), std::end(words), "five"), end_iter, std::begin(words_copy));
std::copy (std::begin (words_copy),end_copy_iter, std::ostream_iterator<string>{std::cout," "});
std::cout << std::endl;
```

生成的 words_copy 容器默认有 20 个元素。rotate_copy() 算法现在会将现有元素的旋转序列保存到 words_copy 中。在输出时，这个算法返回的迭代器可以用来确定 words_copy 的尾部边界；如果没有它，就必须通过源序列的元素个数来计算出尾部边界。