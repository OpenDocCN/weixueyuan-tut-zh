# C++ tuple（STL tuple）模板用法详解

tuple<> 模板是 pair 模板的泛化，但允许定义 tuple 模板的实例，可以封装不同类型的任意数量的对象，因此 tuple 实例可以有任意数量的模板类型参数。tuple 模板定义在 tuple 头文件中。

tuple 这个术语也适用于很多其他的场景，例如数据库，这里一个 tuple 就是由一些类型的不同数据项组成的，这和 tuple 的概念相似。tuple 对象有很多用途。当需要将多个对象当作一个对象传给函数时，tuple 类型是很有用的。

## tuple 的操作

生成 tuple 对象的最简单方式是使用定义在 tuple 头文件中的辅助函数 make_tuple()。这个函数可以接受不同类型的任意个数的参数，返回的 tuple 的类型由参数的类型决定。例如：

```
auto my_tuple = std::make_tuple (Name{"Peter”，"Piper"},42,std::string{"914 626 7890"})；
```

my_tuple 对象是 tuple<Name，int，string> 类型，因为模板类型参数是由 make_tuple() 函数的参数推导出来的。如果提供给 make_tuple() 的第三个参数是一个字符串常量，my_tuple 的类型将是 tuple<Name,int,const*>，这和之前的不同。

tuple 对象的构造函数提供了可能会用到的每一种选择。例如：

```
std::tuple<std::string, size_t> my_tl;//Default initialization
std:: tuple<Name, std::string> my_t2 {Name {"Andy", "Capp"},std::string{“Programmer”}};
std::tuple<Name,std::string> copy_my_t2{my_t2}; // Copy constructor
std::tuple<std::string, std::string> my_t3 {"this", "that"};
// Implicit conversion
```

tuple 中的对象由默认构造函数用默认值初始化。为 my_t2 调用的构造函数将参数移到 tuple 的元素中。下一条语句会调用拷贝构造函数来生成 tuple，在最后一个构造函数调用中，将参数隐式转换为 string 类型并生成了一个 tuple 元素。

也可以用 pair 对象构造 tuple 对象，pair 可以是左值，也可以是右值。显然，tuple 只能有两个元素。下面有两个示例：

```
auto the_pair = std::make_pair("these","those");
std::tuple<std::string, std::string> my_t4 {the_pair}; std::tuple<std::string, std::string> my_t5 {std::pair <std::string, std::string > { "this", "that"}};
```

第二条语句从 the_pair 生成了一个 tuple，它是一个左值。the_pair 的成员变量 first 和 second 可以隐式转换为这个 tuple 中的元素的类型。最后一条语句从右值 pair 对象生成了一个 tuple。

可以用任何比较运算符来比较相同类型的 tuple 对象。tuple 对象中的元素是按照字典顺序比较的。例如：

```
std::cout << std::boolalpha << (my_t4 < my_t5) << std::endl;
```

tuple 对象中的元素是依次比较的，第一个不同的元素决定了比较结果。my_t4 的第一个元素小于 my_t5 的第一个元素，因此比较结果为 true。如果是相等比较，任何一对不相等的对应元素都会使比较结果为 false。

tuple 对象的成员函数 swap() 可以将它的元素和参数交换。参数的类型必须和 tuple 对象的类型一致。例如：

```
my_t4.swap (my_t5);
```

通过调用成员函数 swap() 来交换 my_t4 和 my_t5 对应的元素。显然，tuple 中所有元素的类型都必须是可交换的，tuple 头文件中定义了一个全局的 swap() 函数，它能够以相同的方式交换两个 tuple 对象的元素。

因为 tuple 是 pair 的泛化，所以它的工作方式不同。pair 的对象个数是固定的，因此它们有成员名。tuple 中的对象数目是不固定的，所以访问它们的机制必须能够满足这种情况。函数模板 get<>() 可以返回 tuple 中的一个元素。第一个模板类型参数是 size_t 类型值，它 是 tuple 中元素的索引，因此 0 会选择 tuple 中的第一个元素，1 会选择第二个元素，以此类推。get<>() 模板剩下的类型参数是和 tuple 的参数同样推导的。下面是一个使用 get<>() 和索引来获取元素的示例：

```
auto my_tuple = std::make_tuple (Name {"Peter","Piper"}, 42, std::string {"914 626 7890"});
std::cout << std::get<0>(my_tuple)<< "age = "<<std::get<1>(my_tuple)<< " tel: " << std::get<2>(my_tuple) << std::endl;
```

在输出语句中第一次调用 get<>() 时返回了 my_tuple 中第一个元素的引用，它是一个 Name 对象，第二次调用 get<>() 时返回了下一个元素的引用，它是一个整数；第三次调用 get<>() 时返回了第三个元素的引用，它是一个 string 对象。因此输出结果是：

```
Peter Piper age = 42 tel: 914 626 7890
```

也可以用基于类型的 get<>() 从 tuple 获取元素，但要求 tuple 中只有一个这种类型的元素。例如：

```
auto my_tuple = std::make_tuple(Name{"Peter", "Piper"}, 42, std::string {"914 626 7890"});
std::cout << std::get<Name>(my_tuple)<<" age = " << std::get<int> (my_tuple)<< " tel: " <<std::get<std::string>(my_tuple) << std::endl;
```

如果 tuple 中包含的 get<>() 类型参数值的元素不止一个，代码就无法编译通过。这里 tuple 的全部 3 个成员为不同类型，所以可以正常使用。

全局的 tie<>() 函数模板定义在 tuple 头文件中，它提供了另一种访问 tuple 元素的方式。这个函数可以把 tuple 中的元素值转换为可以绑定到 tie<>() 的左值集合。tie<>() 的模板类型参数是从函数参数中推导的。例如：

```
auto my_tuple = std::make_tuple(Name{"Peter","Piper"}, 42, std::string{"914 626 7890"});
Name name{};
size_t age{};
std::string phone{};
std::tie(name, age, phone) = my_tuple;
```

在最后一条语句中，赋值运算符的左操作数表达式会返回一个参数的 tuple 引用。因此，赋值运算符左右的操作数都是 tuple 对象，并且用 my_tuple 中的元素值来对 tie() 参数中的变量赋值。我们可能并不想存储每一个元素的值。下面展示了如何只保存 my_tuple 中 name 和 phone 的值：

```
std::tie(name, std::ignore,phone) = my_tuple;
```

ignore 定义在 tuple 中，它被用来标记 tie() 函数中要被忽略的值。tuple 中被忽略的元素的值将不会被记录下来。在这个示例中只复制了第一个和第三个元素。

也可以用 tie() 函数来实现对类的数据成员的字典比较。例如，可以在 Name 类中实现 operator<() 函数：

```
bool Name::operator<(const Name& name) const
{
    return std::tie(second, first) < std::tie(name.second, name.first);
}
```

在这个函数体中，调用 tie() 得到的 tuple 对象的元素是按顺序比较的。用 < 运算符来比较连续的元素对，出现的第一对不同值会决定比较的结果；这个表达式的比较结果就是不同元素的比较结果。如果全部元素都相等或等价，那么结果为 false。