# C++ unordered_map 初始化详解

生成 unordered_map 容器和生成 map 一样简单，只要可以用 hash<K> 的实例哈希 k 类型的键，而且必须能够用 == 运算符来比较键。下面展示了如何定义和初始化 unordered_map：

```
std::unordered_map<std::string, size_t> people {{"Jan",44}, {"Jim", 33}, {"Joe", 99}}; // Name,age
```

这样就生成了一个包含 pair<string，size_t> 元素的容器，并用初始化列表中的元素对它进行了初始化。容器中格子的个数是默认的，它使用 equal_to<String>() 对象来判断键是否相等。它会用定义在 string 头文件中的 hash<string> 来对 string 进行哈希。如果没有提供初始值，默认的构造函数会生成一个空容器，它有默认个数的格子。

当我们知道要在容器中保存多少个元素时，可以在构造函数中指定应该分配的格子的个数：

```
std::unordered_map<std::string,size_t> people {{ { "Jan", 44}, {"Jim", 33}, {"Joe", 99}}, 10};
```

这个构造函数有两个参数：初始化列表和需要分配的格子数。

也可以用迭代器定义的一段 pair 对象来生成容器。显然，只要这个范围内的 pair 对象都是要求的类型，那么任何对象源都可以接受。例如：

```
std::vector<std::pair<string, size_t>>folks {{ "Jan",44}, {"Jim", 33}, {"Joe", 99},{"Dan", 22},{"Ann", 55}, {"Don", 77}};
std::unordered_map<string, size_t> neighbors {std::begin(folks), std::end(folks) , 500};
```

folks 是一个包含 pair<string,size_t> 类型元素的 vector 容器，然后用它的元素来填充 neighbors 容器。这里为 neighbors 分配了 500 个格子，但也可以省略这个参数，使用默认的格子个数。可以为前面的两个构造函数指定定义哈希函数的函数对象。这个函数对象会分别作为第 1 个构造函数的第 3 个参数，以及第 2 个构造函数的第 4 个参数，所以这时需要为第 2 个构造函数指定格子个数。接下来会展示如何在接收初始化列表的构造函数中指定这个参数。

假如我们想要用定义在前面章节中的 Name 对象作为键，那就必须为它定义一个哈希函数和一个恒等运算符，扩展后的类的定义如下：

```
class Name
{
public:
    size_t hash() const { return std::hash<std::string>()(first+second); }
    bool operator==(const Name& name) const { return first == name.first && second== name.second; }
};
```

在这个示例中，编译器提供的默认的 operator==() 成员函数能够满足我们的要求，但还是想自己定义。成员函数 hash() 用函数对象 hash<string>() 来哈希 Name 对象的成员 first 和 second 所拼接的字符串。

unordered_map 容器的哈希函数只能接受和键同类型的单个参数，它会返回一个 size_t 类型的哈希值。我们可以定义一个满足这些条件的函数对象的类型，这个类型的函数对象会调用 Name 对象的成员函数 hash():

```
class Hash_Name {
public:
    size_t operator()(const Name& name) const { return name.hash(); }
};
```

当生成 unordered_map 容器时，可以用 Hash_Name 对象作为它的比较函数：

```
std::unordered_map<Name, size_t, Hash_Name> people
{{{{"Ann", "Ounce"}, 25}, {{"Bill", "Bao"}, 46}, {{"Jack", "Sprat"}, 77}},500,Hash Name()}
```

这个容器中的元素是 pair<Name, size_t> 类型对象。它的构造函数的第一个参数是一个初始化列表，里面定义了三个这种类型的对象。注意括号是如何嵌套的。最内层的括号中包含 Name 构造函数的参数。它上面的一层包含的是 pair<Name，size_t> 构造函数的参数。

unordered_map 构造函数的第 2 个参数是格子的个数，我们必须指定它，因为我们想使用第 3 个参数，第 3 个参数是用来哈希键的函数对象。Hash_Name 类型的函数对象会作为这个容器的第 3 个模板类型参数。这么做是必要的，因为模板类型参数有一个不同于我们函数对象的类型的默认值。unordered_map 有以元素段为参数的构造函数，它的前两个参数是迭代器，第 3 个参数是格子个数，第 4 个参数是哈希函数。

当需要指定用来比较两个键对象是否相等的函数对象时，必须指定格子个数，函数对象会用键值来生成哈希值。如果我们忽略了 Name 类的成员函数 operator==()，并且假设定义了一个定义了函数对象的类类型 Name_Equal，可以按如下方式在构造函数中指定它：

```
std::unordered_map<Name, size_t, Hash_Name, Name_Equal〉 people
{ { { {"Ann", "Ounce"}, 25}, {{"Bill”, "Bao"}, 46},{{"Jack","Sprat"}, 77}},500,Hash_Name(), Name_Equal()};
```

这里有一个额外的模板类型参数和一个额外的构造函数参数，因为参数有默认值，所以这个模板类型参数是必要的。模板参数列表中用来比较键的函数对象同样会用在以初始化列表为参数的构造函数中。

unordered_map 也有移动和拷贝构造函数。显然，可以用它们生成容器的副本，副本容器的格子个数、哈希函数都和参数容器相同。