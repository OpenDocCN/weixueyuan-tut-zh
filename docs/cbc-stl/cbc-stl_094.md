# C++ search_n（STL search_n）搜索算法详解

search_n() 算法会搜索给定元素的匹配项，它在序列中连续出现了给定的次数。它的前两个参数是定义搜索范围的正向迭代器，第 3 个参数是想要查找的第 4 个元素的连续匹配次数。例如：

```
std::vector<double> values {2.7, 2.7, 2.7, 3.14, 3.14, 3.14, 2.7, 2.7};
double value {3.14};
int times {3};
auto iter = std::search_n(std::begin(values), std::end(values), times, value);
if (iter != std::end(values))
    std::cout << times << " successive instances of " << value<< " found starting index " << std::distance (std::begin (values), iter) <<std::endl;
```

这段代码会在 values 容器中查找第一个有 3 个 value 实例的匹配项。它找到的序列的索引为 3。注意用来指定个数的第三个参数必须是无符号整型；如果不是，编译这段代码会产生警告。

这里用 == 来比较元素，但也可以提供额外的断言参数来代替它。当然，这不需要定义一个判断是否相等的比较。下面是一个完整的示例：

```
// Searching using search_n() to find freezing months
#include <iostream>                              // For standard streams
#include <vector>                                // For vector container
#include <algorithm>                             // For search_n()
#include <string>                                // For string class
using std::string;

int main()
{
    std::vector<int> temperatures {65, 75, 56, 48, 31, 28, 32, 29, 40, 41, 44, 50};
    int max_temp {32};
    int times {3};
    auto iter = std::search_n(std::begin(temperatures), std::end(temperatures), times, max_temp,[](double v, double max){return v <= max; });
    std::vector<string> months {"January", "February", "March", "April", "May", "June","July", "August", "September", "October", "November", "December"};
    if(iter != std::end(temperatures))
        std::cout << "It was " << max_temp << " degrees or below for " << times<< " months starting in " << months[std::distance(std::begin(temperatures), iter)]<< std::endl;
}
```

temperatures 容器中保存了一年中每个月份的平均温度。search_n() 的最后一个参数是一个 lambda 表达式的谓词，当元素小于等于 max_temp 时，它会返回 true。month 容器中保存月份的名称。

表达式 std::distance(std::begin(temperatures)，iter) 会返回 temperatures 这个序列的第一个元素的索引，这个序列可以使谓词返回 true。这个值用来索引选择 months 中月份的名称，所以这段代码会输出如下内容：

It was 32 degrees or below for 3 months starting in May