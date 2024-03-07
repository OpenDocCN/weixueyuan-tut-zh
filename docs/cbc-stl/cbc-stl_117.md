# C++ swap_ranges(STL swap_ranges)函数使用详解

可以用 swap_ranges() 算法来交换两个序列。这个算法需要 3 个正向迭代器作为参数。前两个参数分别是第一个序列的开始和结束迭代器，第三个参数是第二个序列的开始迭代器。显然，这两个序列的长度必须相同。这个算法会返回一个迭代器，它指向第二个序列的最后一个被交换元素的下一个位置。例如：

```
using Name = std::pair<string, string>; // First and second name
std::vector<Name> people {Name{"Al", "Bedo" }, Name { "Ann", "Ounce"}, Name{"Jo"，"King"}};
std::list<Name> folks {Name{"Stan", "Down"}, Name{"Dan","Druff"},Name {"Bea", "Gone"}};
std::swap_ranges(std::begin(people), std::begin(people) + 2, ++std::begin(folks));
std::for_each(std::begin(people), std::end(people),[](const Name& name) {std: :cout << '"' << name.first << " " << name.second << "\" ";});
std::cout << std::endl; // "Dan Druff" "Bea Gone" "Jo King"
std::for_each (std::begin (folks) , std::end (folks) ,[] (const Name& name){std::cout << '"' << name.first << " " << name.second << "\" "; });
std::cout << std::endl;// "Stan Down" "Al Bedo" "Ann Ounce"
```

这里使用 vector 和 list 容器来保存 pair<string,string> 类型的元素，pair<string,string> 用来表示名称。swap_ranges() 算法被用来交换 people 的前两个元素和 folks 的后两个元素。这里并没有为了将 pair 对象写入流而重载 operator<<() 函数，因此 copy() 无法用输出流迭代器来列出容器的内容。为了生成输出，选择使用 for_each() 算法将 lambda 表达式运用到容器的每个元素上。这个 lambda 表达式只会将传给它的 Name 元素的成员变量写入标准输出流。注释展示了执行这段代码后输出的结果：

定义在 utility 头文件中的 swap() 算法的重载函数的模板原型为：

template<typename T1, typename T2> void swap(std::pair<T1,T2> left, std::pair<T1,T2> right);

这段代码会对 pair<T1,T2> 对象进行交换，在前面的代码段中也可以用 swap_ranges() 来交换元素。

用来交换两个 T 类型对象的 swap() 模板也被定义在 utility 头文件中。除了 pair 对象的重载之外，utility 文件头中也有可以交换任何类型的容器对象的模板的重载。也就是说，可以交换两个 list<T> 容器或者两个 set<T> 容器但不能是一个 list<T> 和 vector<T>，也不能是一个 list<T1> 和一个 list<T2>。

另一个 swap() 模板的重载可以交换两个相同类型的数组。也有其他几个 swap() 的重载，它们可以用来交换其他类型的对象，包含元组和智能指针类型，正如本章前面所述。iter_swap() 算法有一些不同，它会交换两个正向迭代器所指向的元素。