# C++ pair(STL pair)类模板的用法详解

我们已经知道 pair<const K, T> 对象是如何封装键及其关联的对象，也了解了 pair<const K, T> 对象是如何表示 map 容器中的元素的。一般来说，pair 对象可以封装任意类型的对象，可以生成任何想生成的 pair<T1,T2> 对象，可以是数组对象或者包含 pair<T1,T2> 的 vector 容器。例如，pair 可以封装两个序列容器或两个序列容器的指针。pair<T1，T2> 模板定义在 utility 头文件中，如果不想使用 map 而只想使用 pair 对象，可以包含这个头文件。

## pair 的操作

考虑到 pair 是一个比较简单的模板类型，它只有两个 public 数据成员 first 和 second。令人惊讶的是，它却可以构造各种不同的 pair<T1，T2>。我们已经知道如何使用 first 和 second 来创建对象。

和右值引用参数一样，pair 也有很多版本的引用参数，而且有一些版本的右值引用参数允许参数隐式转换为所需的类型。例如，下面有 4 种不同的方式来创建一个 pair 对象：

```
std::string s1 {"test”}, s2{"that"};
std::pair<std::string, std::string> my_pair{s1, s2};
std::pair<std::string, std::string> your_pair{std::string {"test"},std::string {"that"}};
std::pair<std::string, std::string> his_pair{"test", std::string {"that"}};
std::pair<std::string, std::string> her_pair{"test", "that"};
```

第一个 pair 构造函数复制了所有参数的值，第二个移动参数值，第三个为了隐式转换而将第一个参数传给 string 的构造函数，最后一个构造函数将两个参数隐式转换为 string 对象而且它们会被移到 pair 的成员变量 first 和 second 中。由于这个构造函数有右值引用参数版本，因此任意一个或两个模板类型参数可以是 unique_ptr<T>。

make_pair<T1，T2> 函数模板是一个辅助函数，可以生成并返回一个 pair<T1，T2> 对象。 可以如下所示生成先前代码块中的 pair 对象：

```
auto my_pair = std::make_pair(s1, s2);
auto your_pair = std::make_pair(std::string {"test"},std::string {"that"});
auto his_pair = std::make_pair<std::string, std::string>("test",std::string {"that"});
auto her_pair = std::make_pair<std::string, std::string>("test", "that");
```

前两条语句中的函数模板的类型参数由编译器推断。在最后两条语句中，类型是明确的。如果在最后两条语句中忽略模板类型参数，那么对象类型将是 pair<const char*，string> 和 pair<const char*, const char*>。

pair 对象也可以复制或移动构造它的成员变量。例如：

```
std::pair<std::string, std:: string> new_pair{my_pair}; // Copy constructor
std::pair<std::string, std::string>
    old_pair{std::make_pair(std::string{"his"},std::string{"hers"})};
```

old_pair 是由 pair<string,string> 类的移动构造函数生成的。

另一个 pair 构造函数使用了 C++11 引入的这种机制，它允许通过在适当的位置生成 first 和 second 对象来构建 pair<T1, T2>。T1 和 T2 的构造函数的参数作为 tuple 参数传给 pair 的构造函数。下一节会介绍如何使用 tuple 对象。下面是一个使用 pair 对象的示例：

```
std::pair<Name, Name> couple{std::piecewise_construct, std:: forward_as_tuple ("Jack","Jones") , std:: forward_as_tuple ("Jill", "Smith")};
```

这里，pair 构造函数的第一个参数是一个定义在 utility 头文件中的 piecewise_construct 类型的实例，这是一个用来作为标签或标记的空类型。这个 piecewise_constmct 参数唯一的作用是区分这个构造函数的调用和有两个 tuple 参数的构造函数调用之间的区别，后者的两个参数通常用来作为 pair 成员变量 first 和 second 的值。

这里，构造函数的第二和第三个参数指定了构造 first 和 second 对象的参数集，forward_as_tuple() 是一个定义在 tuple 头文件中的函数模板。这里用它的转发参数生成了一个 tuple 引用。不会经常用到这种 pair 的构造函数，但它为不支持拷贝或移动运算的 T1 和 T2 类型提供了在适当位置生成 pair<T1,T2> 对象的独特能力。

注意，如果参数是一个临时对象，forward_as_tuple() 函数会生成一个右值引用的 tuple。 例如：

```
int a {1}, b {2};
const auto& c = std::forward_as_tuple(a,b);
```

这里 c 的类型是 tuple<int&,int&>，因此成员变量是引用。但是假设这样写声明的话：

```
const auto& c = std::forward_as_tuple(1,2);
```

这里 c 的类型是 tuple<int &,int&>，成员变量作为值引用。
如果成员变量可以被复制和移动，pair 对象就支持复制和移动赋值。例如：

```
std::pair<std::string, std::string> old_pair; // Default constructor
std::pair<std::string, std::string> new_pair {std::string{"his"} , std::string{"hers"}};
old_pair = new_pair; // Copy assignment
new_pair = pair<std::string, std::string>
{std::string{"these"}, std::string{"those"}}; // Move assignment
```

默认的 pair 构造函数会用它的成员变量，即空的 string 对象来生成 old_pair 这是一个空的字符串对象。第 3 条语句一个成员一个成员地将 new_pair 复制到 old_pair 中。第 4 条语句将作为赋值运算符的右操作数的 pair 对象的成员变量移到 new_pair 中。

当 pair 对象包含不同类型的成员变量时，也可以将一个 pair 对象赋值给另一个 pair 对象，只要作为右操作数的 pair 对象的成员变量可以隐式转换为左操作数的 pair 对象的成员变量的类型。例如：

```
auto prl = std::make_pair ("these", "those"); // Type pair<const char*, const char*>
std::pair<std::string, std::string> pr2; // Type pair<string, string>
pr2 = prl; // OK in this case
```

prl 成员变量 first 和 second 的类型是 const char*。这个类型可以隐式转换为 string，即 pr2 成员变量的类型，因此可以成功赋值。如果这些类型不能隐式转换，这条赋值语句就无法通过编译。

pair 对象有全套的运算符 ==、!=、<、<=、>、>=。这些运算符都可以正常使用，作为操作数的 pair 对象的类型必须是相同的，它们的成员变量的比较方式也必须相同。相等运算符返回 true，如果左右操作数的成员变量相等的话：

```
std::pair<std::string, std::string> new_pair;
new_pair.first = "his";
new_pair.second = "hers";
if (new_pair == std::pair<std::string, std::string> {"his", ,"hers"})
    std::cout << "Equality!\n";
```

new_pair 的成员变量 first 和 second 被赋值为右操作数所包含的字符串。如果 pair 对象是相等的，if 语句会输出一些消息。当两个 pair 对象中的任何一个或两个成员不相等时，!= 比较会返回 true。

对于小于或大于比较，pair 对象的成员变量是按字典顺序比较的。如果 new_pair.first 小于 old_pair.first 的话，表达式 new_pair<old_pair 会返回 true。如果它们的成员变量 first 相等，但  new_pair.second 小于 old_pair.second，new_pair < old_pair 也为 true。下面是一个 示例：

```
std::pair<int, int> p1 {10, 9};
std::pair<int, int> p2 {10, 11};
std::pair<int, int> p3 {11, 9};
std::cout<<std::boolalpha << (p1 < p2) <<" "<<(pi > p3) << " "<< (p3 > p2) << std::endl;
```

第一个比较的结果为 true，因为 p1 和 p2 的成员变量 first 相等，p1 的成员变量 second 小于 p2 的成员变量 second。
第二个比较的结果为 false，因为 p1 的 first 小于 p3 的 first。 第三个比较的结果则为 true，因为 p3 的 first 大于 p2 的 first。

pair 的成员函数 swap() 可以和作为参数传入的另一个 pair 对象交换其成员变量 first 和 second。显然，参数必须是相同类型。下面有一个示例：

```
std::pair<int, int> p1 {10, 11};
std::pair<int, int> p2 {11, 9};
p1.swap(p2); // p1={ll,9} p2={10/11}
```

如果执行两次 swap()，对象恢复成原来的值。