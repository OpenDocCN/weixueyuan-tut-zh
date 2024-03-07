# C++ copy_if(STL copy_if)算法详解

copy_if() 算法可以从源序列复制使谓词返回 true 的元素，所以可以把它看作一个过滤器。前两个参数定义源序列的输入迭代器，第三个参数是指向目的序列的第一个位置的输出迭代器，第 4 个参数是一个谓词。会返回一个输出迭代器，它指向最后一个被复制元素的下一个位置。下面是一个使用 copy_if() 的示例：

```
std::vector<string> names {"A1", "Beth", "Carol", "Dan", "Eve","Fred", "George", "Harry", "Iain", "Joe"};
std::unordered_set<string> more_names {"Jean", "John"};
size_t max_length{4};
std::copy_if(std::begin(names), std::end(names), std::inserter(more_names, std::begin(more_names)), max_length { return s.length() <= max_length;});
```

因为作为第 4 个参数的 lambda 表达式所添加的条件，这里的 copy_if() 操作只会复制 names 中的 4 个字符串或更少。目的容器是一个 unordered_set 容器 more_names，它已经包含两个含有 4 个字符的名称。和前面的章节一样，insert_itemtor 会将元素添加到限定的关联容器中。如果想要展示它是如何工作的，可以用 copy() 算法列出 more_names 的内容：

```
std::copy(std::begin(more_names), std::end(more_names), std::ostream iterator <string>{std::cout, " "});
std::cout << std::endl;
```

当然，copy_if() 的目的容器也可以是一个流迭代器：

```
std::vector<string> names { "Al", "Beth", "Carol", "Dan", "Eve","Fred", "George", "Harry", "Iain", "Joe"};
size_t max_length{4};
std::copy_if(std::begin(names), std::end(names), std::ostream iterator< string> {std::cout," "}, max_length { return s.length() > max_length; });
std::cout << std::endl;
```

这里会将 names 容器中包含的含有 4 个以上字符的名称写到标准输出流中。这段代码会输出如下内容：

Carol George Harry

输入流迭代器可以作为 copy_if() 算法的源，也可以将它用在其他需要输入迭代器的算法上。例如：

```
std::unordered_set<string> names;
size_t max_length {4};
std::cout << "Enter names of less than 5 letters. Enter Ctrl+Z on a separate line to end:\n";
std::copy_if(std::istream_iterator<string>{std::cin},std:: istream iterator<string>{}, std::inserter(names, std::begin (names)),max_length { return s.length() <= max_length; });
std::copy(std::begin(names), std::end(names), std::ostream_iterator <string>{std::cout," "});
std::cout << std::endl;
```

容器 names 最初是一个空的 unordered_set。只有当从标准输入流读取的姓名的长度小于或等于 4 个字符时，copy_if() 算法才会复制它们。执行这段代码可能会产生如下输出：

Enter names of less than 5 letters. Enter Ctrl+Z on a separate line to end:
Jim Bethany Jean Al Algernon Bill Adwina Ella Frederick Don ^Z
Ella Jim Jean Al Bill Don

超过 5 个字母的姓名可以从 cin 读入，但是被忽略掉，因为在这种情况下第 4 个参数 的判定会返回 false。因此，输入的 10 个姓名里面只有 6 个会被存储在容器中。