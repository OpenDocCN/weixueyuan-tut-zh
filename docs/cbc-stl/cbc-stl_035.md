# C++ list(STL list)排序及合并元素方法详解

sort() 函数模板定义在头文件 algorithm 中，要求使用随机访问迭代器。但 list 容器并不提供随机访问迭代器，只提供双向迭代器，因此不能对 list 中的元素使用 sort() 算法。但是，还是可以进行元素排序，因为 list 模板定义了自己的 sort() 函数。sort() 有两个版本：无参 sort() 函数将所有元素升序排列。第二个版本的 sort() 接受一个函数对象或 lambda 表达式作为参数，这两种参数都定义一个断言用来比较两个元素。

下面是一个以断言作为参数调用 list 成员函数 sort() 的示例：

```
names.sort(std::greater<std::string>()); // Names in descending sequence
```

这里使用了定义在头文件 functional 中的模板 greater<T>。这个模板定义了一个用来比较 T 类型对象的函数对象。如果第一个参数大于第二个参数，那么成员函数 operator()() 返回 true。头文件 functional 中定义了一些定义了断言的模板，在后面，我们会看到更多的断言。排序执行完毕后，list 中的元素如下：
"Jules" "Jim" "Janet" "Jane" "Hugo" "Hannah" "Ann" "Alan"

因此，现在 names 中的元素是降序排列的。有一个简洁版的 greater<T> 断言，可以如下所示使用：

```
names.sort(std::greater<>()); // Function object uses perfect forwarding
```

简洁版的函数对象可以接受任何类型的参数，使用完美转发 (perfect forwarding) 可以避免不必要的参数拷贝。因此，完美转发总是会快很多，因为被比较的参数会被移动而不是复制到函数中。

当然，在必要时可以将自定义的函数对象传给断言来对 list 排序。尽管对一般对象来说，并不需要这样。如果为自己的类定义了 operator()()，然后就可以继续使用 std::greater<>。 当我们需要比较非默认类型时，就需要一个函数对象。

例如，假设我们想对 names 中的元素进行排序，但是不想使用字符串对象标准的 std::greater<> 来比较，而是想将相同初始字符的字符串按长度排序。可以如下所示定义一个类：

```
// Order strings by length when the initial letters are the same
class my_greater
{
    public:
    bool operator () (const std::strings s1, const std::strings s2)
    {
        if (s1[0] == s2 [0])
            return si.length() > s2.length();
        else
            return s1 > s2;
    }
};
```

可以用这个来对 names 中的元素排序：

```
names.sort(my_greater()); // Sort using my_greater
```

代码执行完毕后，list 中包含的元素如下：
"Jules" "Janet" "Jane” "Jim" "Hannah" "Hugo" "Alan" "Ann"

这个结果和前面使用字符串对象标准比较方式的结果明显不同。names 中初始字符相同的元素按长度降序排列。当然，如果不需要重用 my_greater() 断言，这里也可以使用 lambda 表达式。

下面是一个用 lambda 表达式实现的示例：

```
names.sort([](const std::strings s1, const std::strings s2)
{
    if (s1[0] == s2[0])
        return s1.length() > s2.length();
    else
        return s1 > s2;
});
```

这和前面代码的作用相同。

list 的成员函数 merge() 以另一个具有相同类型元素的 list 容器作为参数。两个容器中的元素都必须是升序。参数 list 容器中的元素会被合并到当前的 list 容器中。例如：

```
std::list<int> my_values {2, 4, 6, 14};
std::list<int> your_values{ -2, 1, 7, 10};
my_values.merge (your_values);//my_values contains: -2 1 2 4 6 7 10 14
your_values.empty(); // Returns true
```

元素从 your_values 转移到 my_values，因此，在执行完这个操作后，your_values 中就没有元素了。改变每个 list 节点的指针，在适当的位置将它们和当前容器的元素链接起来，这样就实现了元素的转移。list 节点在内存中的位置不会改变；只有链接它们的指针变了。在合并的过程中，两个容器中的元素使用 operator()() 进行比较。

在另一个版本的 merge() 函数中，可以提供一个比较函数作为该函数的第二个参数，用来在合并过程中比较元素。例如：

```
std::list<std::string> my_words { "three","six", "eight"};
std::list<std::string> your_words { "seven", "four", "nine"};
auto comp_str = [](const std::strings s1, const std::strings s2){ return s1[0]<s2[0];};
my_words.sort (comp_str); //"eight" "six" "three"
your_words.sort (comp_str) ;  //"four" "nine" "seven"
my_words.merge (your_words, comp_str) ; // "eight" "four" "nine" "six" "seven" "three"
```

这里的字符串对象比较函数是由 lambda 表达式定义的，这个表达式只比较第一个字符。比较的效果是，在合并的 list 容器中，"six”在”seven”之前。在上面的代码中，也可以无参调用 merge()，这样"seven"会在"six"之前，这是一般的排序。

list 容器的成员函数 splice() 有几个重载版本。这个函数将参数 list 容器中的元素移动到当前容器中指定位置的前面。可以移动单个元素、一段元素或源容器的全部元素。下面是一个剪切单个元素的示例：

```
std::list<std::string> my_words {"three", "six", "eight"};
std::list<std::string> your_words {"seven", "four", "nine"};
my_words.splice(++std::begin(my_words), your_words, ++std::begin(your_words));
```

splice() 的第一个参数是指向目的容器的迭代器。第二个参数是元素的来源。第三个参数是一个指向源 list 容器中被粘接元素的迭代器，它会被插入到第一个参数所指向位置之前。代码执行完中后，容器中的内容如下：

```
your_words: "seven", "nine"
my_words : "three", "four", "six", "eight"
```

当要粘接源 list 容器中的一段元素时，第 3 和第 4 个参数可以定义这段元素的范围。 例如：

```
your_words.splice(++std::begin(your_words),my_words,++std::begin(my_words), std::end(my_words));
```

上面的代码会将 my_words 从第二个元素直到末尾的元素，粘接到 your_words 的第二个元素之前。上面两个 list 容器的内容如下：

```
your_words:"seven", "four", "six", "eight","nine" my_words: "three"
```

下面的语句可以将 your_words 的全部元素粘接到 my_words 中：

```
my_words.splice(std::begin(my_words), your_words);
```

your_words 的所有元素被移到了 my_words 的第一个元素"three”之前。然后，your_words 会变为空。即使 your_words 为空，也仍然可以向它粘接元素：

```
your_words.splice(std::end(your_words), my_words);
```

现在，my_words 变为空，your_words 拥有全部元素。第一个参数也可以是 std::begin (your_words)，因为当容器为空时，它也会返回一个结束迭代器。