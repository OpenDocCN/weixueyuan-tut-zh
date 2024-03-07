# C++ find（STL find）查找算法详解

find() 为在输入迭代器所定义的范围内查找单个对象的算法，它可以在前两个参数指定的范围内查找和第三个参数相等的第一个对象。

find 算法会返回一个指向被找到对象的迭代器，如果没有找到对象，会返回这个序列的结束迭代器。下面展示了如何使用 find():

```
std::vector<int> numbers {5, 46, -5, -6, 23, 17, 5, 9, 6, 5};
int value {23};
auto iter = std::find(std::begin(numbers), std::end(numbers), value);
if (iter != std:: end (numbers))
    std::cout << value << " was found. \n";
```

这段代码会输出一条在 numbers 中找到 23 的消息，当然，可以反复调用 find() 来找出这个序列中所有给定元素的匹配项：

```
size_t count {};
int five {5};
auto start_iter = std::begin(numbers);
auto end_iter = std::end(numbers);
while((start_iter = std::find(start_iter, end_iter, five)) != end_iter)
{
    ++count;
    ++start_iter;
}
std::cout << five << " was found " << count << " times." << std::endl; // 3 times
```

在 while 循环中，count 变量会通过自增来记录 five 在 vector 容器 numbers 中的发现次数。循环表达式调用 find()，在 start_iter 和 end_iter 定义的范围内查找 five。find() 返回的迭代器被保存在 start_ter 中，它会覆盖这个变量先前的值。最初，find() 会搜索 numbers 中的所有元素，因此 find() 会返回一个指向 five 的第一个匹配项的迭代器。

每次找到 five，循环体中的 startjter 都会自增，因此它会指向被找到元素的后一个元素。所以，下一次遍历搜索的范围是从这个位置到序列末尾。当不再能够找到 five 时，find() 会返回 end_iter，循环结束。