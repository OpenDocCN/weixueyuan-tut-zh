# C++ partition(STL partition)算法使用详解

在序列中分区元素会重新对元素进行排列，所有使给定谓词返回 true 的元素会被放在所有使谓词返回 false 的元素的前面。这就是 partition() 算法所做的事。

partition 的前两个参数是定义被分区序列范围的正向迭代器，第三个参数是一个谓词。下面展示如何使用 partition() 来重新排列序列中的值，所有小于平均值的元素会被放在所有大于平均值的元素的后面：

```
std::vector<double> temperatures {65, 75, 56, 48, 31, 28, 32, 29, 40, 41, 44, 50};
std::copy(std::begin(temperatures), std::end(temperatures), //List the values
std::ostream_iterator<double>{std::cout, " "});
std::cout << std::endl;
auto average = std::accumulate(std::begin(temperatures),std::end(temperatures), 0.0)/temperatures.size();
std::cout << "Average temperature: "<< average << std::endl;
std::partition(std::begin(temperatures), std::end(temperatures),average { return t < average; });
std::copy(std::begin(temperatures), std::end(temperatures),std::ostream_iterator<doiible>{std::cout, " "});
std::cout << std::endl;
```

这段代码会输出下面这些内容：

65 75 56 48 31 28 32 29 40 41 44 50
Average temperature: 44.9167
44 41 40 29 31 28 32 48 56 75 65 50

通过 accumulate() 算法创建的元素之和除以元素个数，计算出 temperatures 容器中元素的平均值。在前面，我们已经介绍了 accumulate() 算法，所以，我们记得它的第三个参数就是和的初始值。执行 partition() 算法后，可以看到所有小于平均值的温度值都在大于平均值的温度值之前。

这个谓词可以不必是用来处理顺序关系的一它可以是我们喜欢的任何样子。例如，可以对表示个体的 Person 对象进行分区，将所有女性放在男性的前面，或者将有大学学历的放在没有大学学历的前面。下面是一个对 tuple 对象的序列进行分区的示例，这个元组对象用来表示人和标识他们的性别：

```
using gender = char;
using first = string;
using second= string;
using Name = std::tuple<first, second, gender>;
std:: vector<Name> names {std::make_tuple ("Dan", "old", 'm'),std::make_tuple("Ann", "old", 'f'),std::make_tuple ("Ed", "old",'m'),std::make_tuple ("Jan", "old", 'f'), std::make_tuple ("Edna", "old", 'f')};
std::partition(std::begin(names), std::end(names),[](const Names name) { return std::get<2>(name) == 'f'; });

for(const auto& name : names)
    std:: cout << std::get<0> (name) << " "<< std::get<1> (name) << std::endl;
```

这里使用 using 声明来解释 tuple 对象成员变量的意义。当 tuple 对象的最后一个成员变量是“f”时，这个谓词会返回 true，所以输出中会出现 Edna、Ann 以及处在 Ed 和 Dan 之前的 Jan。在这个谓词中，可以用表达式 std::get<gender>(name) 来引用 tuple 的第三个成员变量。这样做是可行的，因为第三个成员是唯一的，这就允许用它的类型来识别这个成员。

partition() 算法并不保证维持这个序列原始元素的相对顺序。在上面的示例中，对于原 始序列，元素 44 和 41 在 40 的后面。但在进行这项操作之后，它们就不是那样了。为了维持元素的相对顺序，可以使用 stable_partition() 算法。它的参数和 partition() 一样，可以用下面这些语句来代替前一段代码中的 partition() 调用：

```
std::stable_partition(std::begin(temperatures), std::end(temperatures),average { return t < average; });
```

做出这些修改后，对应的输出如下：

65 75 56 48 31 28 32 29 40 41 44 50
Average temperature: 44.9167
31 28 32 29 40 41 44 65 75 56 48 50

可以看到，重排序时并不一定要对序列进行分区，元素的相对顺序被保留了。所有小于平均值的元素的相对顺序都没有被改变，所有大于平均值的元素也是如此。