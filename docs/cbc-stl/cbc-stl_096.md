# C++ partition_copy(STL partition_copy)算法使用详解

partition_copy() 算法以和 stable_partition() 相同的方式对序列进行分区，但那些使谓词返回 true 的元素会被复制到一个单独的序列中，使谓词返回 false 的那些元素会被复制到第三个序列中。这个操作不会改变原始序列。

原始序列由前两个参数指定，它们必须是输入迭代器。第 3 个参数用来确定目的序列的开始位置，它会保存那些使谓词返回 true 的元素。第 4 个参数用来确定另一个目的序列的开始位置，它会保存那些使谓词返回 false 的元素。第 5 个参数是用来分区元素的谓词。下面是一个展示 partition_copy() 用法的完整程序：

```
// Using partition_copy() to find values above average and below average
#include <iostream>                              // For standard streams
#include <vector>                                // For vector container
#include <algorithm>                             // For partition_copy(), copy()
#include <numeric>                               // For accumulate()
#include <iterator>                              // For back_inserter, ostream_iterator

int main()
{
    std::vector<double> temperatures {65, 75, 56, 48, 31, 28, 32, 29, 40, 41, 44, 50};
    std::vector<double> low_t;                       // Stores below average temperatures
    std::vector<double> high_t;                      // Stores average or above temperatures

    auto average = std::accumulate(std::begin(temperatures), std::end(temperatures), 0.0) / temperatures.size();
    std::partition_copy(std::begin(temperatures), std::end(temperatures), std::back_inserter(low_t), std::back_inserter(high_t),average { return t < average; });

    // Output below average temperatures
    std::copy(std::begin(low_t), std::end(low_t), std::ostream_iterator<double>{std::cout, " "});
    std::cout << std::endl;

    // Output average or above temperatures
    std::copy(std::begin(high_t), std::end(high_t), std::ostream_iterator<double>{std::cout, " "});
    std::cout << std::endl;
}
```

这段代码所做的事情和先前介绍的 stable_partition() 相同，但小于平均值的元素会被复制到 low_t 容器中，大于等于平均值的元素会被复制到 high_t 容器中。输出语句可以对此进行验证，它们产生的输出如下：

31 28 32 29 40 41 44
65 75 56 48 50

注意，main() 中的这段代码使用辅助函数 back_inserter() 创建的 back_insert_iterator 对象作为 partition_copy() 调用中两个目的容器的迭代器。back_insert_iterator 通过调用 push_back() 向容器中添加新元素，使用这种方式可以不需要提前知道容器中保存了多少元素。如果对目的序列使用开始迭代器，在执行这个操作前，为了可以复制尽可能多的元素，目的序列中必须有足够的元素。

注意，如果输入序列和输出序列重叠，这个算法将无法正常工作。