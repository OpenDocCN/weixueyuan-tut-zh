# C++ vector 插入元素(数据)详解

通过使用成员函数 emplace()，可以在 vector 序列中插入新的元素。对象会在容器中直接生成，而不是先单独生成对象，然后再把它作为参数传入。

emplace() 的第一个参数是一个迭代器，它确定了对象生成的位置。对象会被插入到迭代器所指定元素的后面。第一个参数后的参数，都作为插入元素的构造函数的参数传入。例如：

```
std::vector<std::string> words {"first", "second"};
// Inserts string(5,'A') as 2nd element
auto iter = words.emplace(++std::begin(words),5,'A');
//Inserts string ("$$$$") as 3rd element
words.emplace(++iter, "$$$$");
```

这段代码执行后，vector 中的字符串对象如下:
"first" "AAAAA" "$$$$" "second"

在 emplace() 的第一个参数的后面，可以使用尽可能多的参数，只要它们是被插入对象的构造函数所需要的。在上面的代码片段中，第一次调用 emplace() 会得到一个由构造函数 string(5,'A') 生成的字符串对象。emplace() 会返回一个指向横入元素的迭代器，被用来在插入元素的后面，插入一个新的元素。

成员函数 insert() 可以在 vector 中插入一个或多个元素。第一个参数总是一个指向插入点的 const 或 non-const 迭代器。元素会被迅速插入到第一个参数所指向元素的前面，如果第一个参数是一个反向迭代器，元素会被插入到迭代器所指向元素的后面。如果选择使用 insert() 来插入元素，稍后会分别阐述每一种可能的情况。会先定义一个 vector，然后列出一个相继调用 insert() 的列表：

```
std::vector<std::string> words { "one","three","eight"} //Vector with 3 elements
```

下面介绍一些使用 insert() 插入单词的方式：

#### 1) 插入第二个参数指定的单个元素

```
auto iter = words.insert(++std::begin(words), "two");
```

在这个示例中，插入点是由 begin() 返回的迭代器递增后得到的。它对应第二个元素，所以新元素会作为新的第二个元素插入，之前的第二个元素以及后面的元素，为了给新的第二个元素留出空间，都会向后移动一个位置。这里有两个 insert 重载版本，它们都可以插入单个对象，其中一个的参数是 constT& 类型，另一个是 T&&类 型——右值引用。因为上面的第二个参数是一个临时对象，所以会调用第二个函数重载版本，临时对象会被移动插入而不是被复制插入容器。

执行完这条语句后，words rector 容器包含的字符串元素为：
"one" "two" "three" "eight"

返回的迭代器指向被插入的元素 string(”two”)。需要注意的是，在使用同样参数的情况下，调用 insert() 没有调用 emplace() 高效。在 insert() 调用中，构造函数调用 string("two")  生成了一个对象，作为传入的第二个参数。在 emplace() 调用中，构造函数用第二个参数直接在容器中生成了字符串对象。

#### 2) 插入一个由第二个和第三个参数指定的元素序列

```
std:: string more[] {"five", "six", "seven" }; // Array elements to be inserted
iter = words.insert(--std::end(words) , std::begin(more), std::end(more));
```

第二条语句中的插入点是一个迭代器，它是由 end() 返回的迭代器递减后得到的。对应最后一个元素，因此新元素会被插入到它的前面。执行这条语句后，words vector 容器中的字符串对象为：
"one" "two" "three" "five" "six" "seven" "eight"

返回的迭代器指向插入的第一个元素"five"。

#### 3) 在 vector 的末尾插入一个元素

```
iter = words.insert(std::end(words), "ten");
```

插入点是最后一个元素之后的位置，因此新元素会被添加到最后一个元素之后。执行完这条语句后，words vector 容器中的字符串对象如下：
"one" "two" "three" "five" "six" "seven" "eight" "ten"

返回的迭代器指向插入的元素"ten”。这和上面的情况 1) 相似；这表明，当第一个参数不指向元素而是指向最后一个元素之后的位置时，它才发挥作用。

#### 4) 在插入点插入多个单个元素。第二个参数是第三个参数所指定对象的插入次数

```
iter = words.insert(std::cend(words)-1, 2, "nine");
```

插入点是最后一个元素，因此新元素 string("nine") 的两个副本会被插入到最后一个元素的前面。

执行完这条语句后，words vector 容器中的字符串对象如下：
"one" "two" "three" "five" "six" "seven" "eight" "nine" "nine" "ten"

返回的迭代器指向插入的第一个元素"nine"。注意，示例中的第一个参数是一个 const 迭代器，这也表明可以使用 const 迭代器。

#### 5) 在插入点，插入初始化列表指定的元素。第二个参数就是被插入元素的初始化列表

```
iter = words.insert(std::end(words), {std::string {"twelve"},std::string {"thirteen"}});
```

插入点越过了最后一个元素，因此初始化列表中的元素被添加到容器的尾部。执行完这条语句后，words vector 容器中的字符串对象如下：
"one" "two" "three" "five" "six" "seven" "eight" "nine" "nine" "ten" "twelve" "thirteen"

返回的迭代器指向插入的第一个元素"twelve"。初始化列表中的值必须和容器的元素类型相匹配。T 类型值的初始化列表是 std::initializer_list<T>，所以这里的 list 类型为 std::initializer_list<std::string>。前面的 insert() 调用中以单词作为参数的地方，参数类型是 std::string，所以单词作为字符串对象的初始值被传入到函数中。

记住，所有不在 vector 尾部的插入点都会有开销，需要移动插入点后的所有元素，从而为新元素空出位置。当然，如果插入点后的元素个数超出了容量，容器会分配更多的内存，这会增加更多额外开销。

vector 的成员函数 insert()，需要一个标准的迭代器来指定插入点；它不接受一个反向迭代器——这无法通过编译。如果需要查找给定对象的最后一个元素，或者在它的后面插入一个新的元素，就需要用到反向迭代器。这里有一个示例：

```
std::vector<std::string> str { "one", "two", "one", "three"};
auto riter = std::find(std::rbegin(str), std::rend(str) , "one");
str.insert(riter.base(), "five");
```

fmd() 算法会在头两个参数所指定的一段元素中，搜索第三个参数指定的元素，返回第一个找到的元素，因此会找到 String("one")。它会返回一个迭代器，这个迭代器和用来指定搜索范围的迭代器有相同的类型，是一个指向匹配元素的反向迭代器。如果没有找到匹配的元素，那么它就是指向第一个元素之前位置的迭代器 rend(str)。使用反向迭代器意味着 fmd()会找到最后匹配的元素；使用标准迭代器会找到第一个匹配的元素，如果没有匹配的元素，会返回 end(str)。

调用 riter 的成员函数 base() 可以得到一个标准迭代器，从序列反方向来看，它指向 riter 前的一个位置，也是朝向序列结束的方向。因为 riter 指向第三个元素，也就是“one”，所以 riter.base() 指向第 4 个元素“three”。如果使用 riter.base() 作为 insert() 的第一个参数，“five”将被插入到这个位置之前，也就是 riter 所指向元素的后面。执行完这些语句后，str 容器会包含下面 5 个字符串元素：
"one", "two", "one", "five", "three"

如果想把插入点变成 fmd() 返回位置的前一个位置，需要将 insert() 的第一个参数变为 iter.base()-1。