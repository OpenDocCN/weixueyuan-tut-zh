# C++ equel_range(STL equal_range)二分查找算法详解

equal_range() 可以找出有序序列中所有和给定元素相等的元素。它的前两个参数是指定序列的两个正向迭代器，第三个参数是要查找的元素。这个算法会返回一个 pair 对象，它有两个正向迭代器成员，其中的 first 指向的是不小于第三个参数的一个元素，second 指向大于第三个参数的一个元素，所以我们也可以通过在单个调用中调用 lower_bound() 和 upper_bound() 来得到同样的结果。可以用下面这些语句来替换前一个代码段中的两条输出语句：

```
auto pr = std::equal_range(std::begin(values) , std::end(values), wanted);
std::cout << "the lower bound for " << wanted << " is " << *pr.first << std::endl;
std::cout << "Tthe upper bound for " << wanted << " is " << *pr.second << std::endl;
```

它和前一段代码的输出完全相同。和前面的二分查找算法一样，equal_range() 也有一个有额外参数的版本，这个参数可以为有序序列提供一些不同于 < 运算符的比较。

前面说过，本节的算法要求它们所处理的序列的元素是有序的，但这并不是全部。所有的二分查找算法都可以用于以特殊方式分区的序列。对于一个给定的希望值，序列中的元素必须按照 (element < wanted) 和 !(wanted < element) 来分区。可以用 equal_range() 二分查找算法来做这些工作，在对 values 容器中的元素执行 equal_range() 之前，可以按如下方式对它进行分区：

```
std::list<int> values {17, 11, 40, 36, 22, 54, 48, 70, 61, 82, 78, 89, 99, 92, 43};
// Output the elements in original order
std::copy(std::begin(values), std::end(values),std::ostream_iterator<int> {std::cout, " "});
std::cout << std::endl;
int wanted {22};    // What we are looking for
std::partition(std::begin(values), std::end(values),wanted { return value < wanted; });
std::partition(std::begin(values), std::end(values),wanted { return !(wanted < value); });
//Output the elements after partitioning
std::copy(std::begin(values), std::end(values),std::ostream_iterator<int>{std::cout," "});
std::cout<< std::endl;
```

这段代码的输出如下：

17 11 40 36 22 54 48 70 61 82 78 89 99 92 43
17 11 22 36 40 54 48 70 61 82 78 89 99 92 43

第一行显示的是元素原始的顺序，第二行显示的是分区之后的顺序。两次分区操作改变了元素的顺序，但改变的并不多，现在我们可以将 equal_range() 应用到 values 容器的元素上，期望值为 wanted:

```
auto pr = std::equal_range(std::begin(values), std::end(values), wanted);
std::cout << "the lower bound for " << wanted << " is " << *pr.first << std::endl;
std::cout << "the upper bound for " << wanted << " is " << *pr.second << std::endl;
```

这段代码和前一段代码的输出是相同的，在前一段代码中，用容器对象的成员函数 sort() 对元素进行了完全排序。本节的所有算法都可以用于以这种方式分区的序列上。显然，如果分区使用的是>，那么在查找算法中使用的函数对象也必须和它保持一致。

在前一段代码中，将 equal_range() 应用到了一个只包含单个期望值的序列中。如果这个序列包含多个实例，pr.first 会指向 wanted 的第一个匹配项，所以 pr.first, pr.second) 这个范围包含的是所有的匹配项。下面是一个示例：

```
// Using partition() and equal_range() to find duplicates of a value in a range
#include <iostream>                              // For standard streams
#include <list>                                  // For list container
#include <algorithm>                             // For copy(), partition()
#include <iterator>                              // For ostream_iterator

int main()
{
    std::list<int> values {17, 11, 40, 13, 22, 54, 48, 70, 22, 61, 82, 78, 22, 89, 99, 92, 43};

    // Output the elements in their original order
    std::cout << "The elements in the original sequence are:\n";
    std::copy(std::begin(values), std::end(values), std::ostream_iterator<int> {std::cout, " "});
    std::cout << std::endl;

    int wanted {22};                                         // What we are looking for

    std::partition(std::begin(values), std::end(values),[wanted { return value < wanted; });
    std::partition(std::begin(values), std::end(values),wanted { return !(wanted < value); });

    // Output the elements after partitioning
    std::cout << "The elements after partitioning are:\n";
    std::copy(std::begin(values), std::end(values), std::ostream_iterator<int> {std::cout, " "});
    std::cout << std::endl;

    auto pr = std::equal_range(std::begin(values), std::end(values), wanted);
    std::cout << "The lower bound for " << wanted << " is " << *pr.first << std::endl;
    std::cout << "The upper bound for " << wanted << " is " << *pr.second << std::endl;

    std::cout << "\nThe elements found by equal_range() are:\n";
    std::copy(pr.first, pr.second, std::ostream_iterator<int> {std::cout, " "});
    std::cout << std::endl;
}
```

输出结果为：

The elements in the original sequence are:
17 11 40 13 22 54 48 70 22 61 82 78 22 89 99 92 43
The elements after partitioning are:
17 11 13 22 22 22 48 70 54 61 82 78 40 89 99 92 43
The lower bound for 22 is 22
The upper bound for 22 is 48

The elements found by equal_range() are:
22 22 22

values 容器中有一些值为 22 的元素，22 也是 wanted 的值。equal_range() 返回了 wanted 在这个序列中的三个实例。这个序列只是被分区了，并不是完全有序的，当序列完全有序时，显然也同样适应。

所以当序列像上边示例那样分区并且不是完全有序时，为什么 equal_range() 会返回 wanted 的所有匹配项？为了弄明白这一点，需要理解 2 个 partition() 调用的作用：

1) 第一个分区操作保证严格小于 wanted 的所有元素都在左分区中，这些元素不需要是有序的。这个操作也保证所有大于等于 wanted 的元素都在右分区中，所以它们都在 wanted 的后面，而且也不需要是有序的。wanted 的所有匹配项都在右分区中，但是和大于 wanted 的元素混合在了一起。前一段代码中，在第一次调用 partition() 后，values 中的元素为：

17 11 13 40 22 54 48 70 22 61 82 78 22 89 99 92 43

17、11、13 是仅有的小于 wanted 的几个值，它们显然在左分区中。分区并不能以任何特别的方式来确定和 wanted 所对应值的位置。22 的全部实例可能出现在右分区的任何位置。

2) 第二个分区操作会被应用到第一个操作的结果上。表达式 !(wanted<value) 等同于 (value <=wanted)。因此作为结果，小于等于 wanted 的所有元素会出现在左分区中，而且所有严格大于 wanted 的元素会在右分区中。这个操作的效果是将所有 wanted 的实例移到左分区中，所以它们被一起作为左分区的最后一个元素。在第二次调用 partition() 后，values 包含的元素如下：

17 11 13 22 22 22 48 70 54 61 82 78 40 89 99 92 43

equal_range() 找到的下边界指向 22 的第一个匹配项，上边界指向 22 的最后一个匹配项的后面，即值为 48 的元素。