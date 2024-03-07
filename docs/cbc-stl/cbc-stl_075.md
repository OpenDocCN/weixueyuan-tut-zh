# C++ unordered_set 删除元素（erase 删除元素）详解

调用 unordered_set 容器的成员函数 clear()可以删除它的全部元素。成员函数 erase()可以删除容器中和传入参数的哈希值相同的元素。另一个版本的 erase()函数可以删除迭代器参数指向的元素。例如，这里不需要大费周章地来删除容器中的元素：

```
std::pair<string, string> person { "John", "Smith"};
auto iter = names.find(person);
if(iter != std::end(names))
    names.erase(iter);
```

erase() 迭代器的参数必须是一个指向容器中元素的、有效的、可解引用的迭代器，因此需要确保它不是容器的结束迭代器。这个版本的 erase() 函数会返回一个指向被删除元素的下一个位置的迭代器，如果删除的是最后一个元素，那么它就是结束迭代器。

如果 person 对象存在，可以像下面这样很容易地删除它：

```
auto n = names.erase(person);
```

这个版本的 erase() 会返回一个 size_t 类型的数作为被删除元素的个数。在这种情况下，这个值只能是 0 或 1，但对于 unordered_multiset 容器来说，这个值可能会大于 1。显然，如果返回值是 0，那么容器中肯定没有这个元素。

尽管最初的示例可能没什么用，但当需要删除一些有特定字符的元素时，可以调用 erase() 来删除迭代器所指向的元素，这是很有用的。假设需要移除 names 容器中名称以字符 'S' 开始的所有元素，那么下面这个循环就可以实现：

```
while(true)
{
    auto iter = std::find_if(std::begin(names), std::end(names),[](const std::pair<string, string>& pr ){ return pr.second[0] == 'S';});
    if(iter == std::end(names))
        break;
    names.erase(iter);
}
```

find_if() 算法的前两个参数定义了一个元素段的范围，它会找到这段元素中第一个可以使第三个参数返回 true 的元素，然后返回这个元素的迭代器。断言的参数必须是从解引用元素段得到的类型对象。这里的元素段是 names 容器中的全部元素，它们都是 pair<string, string> 对象，断言是一个 lambda 表达式，当 pair 的第二个成员的首字母是 'S' 时。当没有元素使 lambda 返回 true 时，算法会返回这段元素的结束迭代器。

这里有另一个版本的 erase()，它可以移除一段元素。下面的语句可以移除除了第一个和最后一个元素之外的所有元素：

```
auto iter = names.erase(++std::begin(names),--std::end(names));
```

这个函数的参数是两个迭代器，它们指定了所删除元素的范围。这个函数会返回一个迭代器，它指向最后一个被删除元素后面的元素。