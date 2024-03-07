# C++ find_end(STL find_end)算法详解

find_end() 会在一个序列中查找最后一个和另一个元素段匹配的匹配项，也可以看作在一个元素序列中查找子序列的最后一个匹配项。这个算法会返回一个指向子序列的最后一个匹配项的第一个元素的迭代器，或是一个指向这个序列的结束迭代器。下面是一个示例：

```
string text {"Smith, where Jones had had \"had\", had had \"had had\"." " \"Had had\" had had the examiners\' approval."};
std::cout << text << std::endl;
string phrase {"had had"};
auto iter = std::find_end(std::begin(text), std::end(text), std::begin (phrase),std::end(phrase));
if(iter != std::end(text))
std::cout << "The last \"" << phrase<< "\" was found at index "<< std::distance (std::begin (text), iter) << std::endl;
```

这段代码会从 text 中搜索“had had”的最后一个匹配项，并输出如下内容：

Smith, where Jones had had "had", had had "had had" . "Had had" had had the examiners' approval.
The last "had had" was found at index 63

可以在 text 中搜索 phrase 的所有匹配项。在这个示例中只会记录匹配项的个数：

```
size_t count {};
auto iter = std::end(text);
auto end_iter = iter;
while((iter = std::find_end(std::begin(text), end_iter, std::begin(phrase), std::end(phrase))) != end_iter)
{
    ++count;
    end_iter = iter;
}
std::cout << "\n\""<< phrase << "\" was found " << count <<" times." << std::endl;
```

这个 while 循环表达式会进行这项搜索工作。循环表达式会在 std::begin(text)，end_iter) 这个范围内搜索 phrase。最开始的搜索范围包含 text 中的全部元素。为了说明这里发生了什么，下面用图 1 来演示这个过程。

![
图 1 用 find_end() 反复搜索
find_end() 返回的迭代器被保存在 iter 中，当它等于 end_iter，即 iter 先前的值时，循环结束。因为 find_end() 会查找子序列的最后一个匹配项，下一个搜索范围的结束迭代器 end_iter 会变为这个算法所返回的迭代器。这个迭代器会指向被找到序列的第一个字符，所以下一次搜索会从 text 的这个点开始，忽略已找到的元素。循环体中 count 自增后，end_iter 被设为 iter。这么做是必要的，因为如果下一次搜索找到 phrase，就会返回这个迭代器。

另一个版本的 find_end() 接受一个用来比较元素的二元谓词作为第 5 个参数，可以用它来重复前面的搜索并忽“大小写：

```
size_t count {};
auto iter = std::end(text);
auto end_iter = iter;
while((iter = std::find_end(std::begin(text), end_iter, std::begin(phrase), std::end (phrase) , [] (char ch1, char ch2) { return std::toupper (ch1) == std::toupper(ch2);})) != end_iter)
{
    ++count;
    end_iter = iter;
}
```

在将字符转换为大写后，会按对比较这两个序列中的元素。在 text 中会找到 phrase 的 5 个实例，因为找到的"Had had"也和 phrase 相等。