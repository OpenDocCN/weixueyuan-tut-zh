# C++ equal(STL equal)比较算法详解

可以用和比较字符串类似的方式来比较序列。如果两个序列的长度相同，并且对应元素都相等，equal() 算法会返回 true。有 4 个版本的 equal() 算法，其中两个用 == 运算符来比较元素，另外两个用我们提供的作为参数的函数对象来比较元素，所有指定序列的迭代器都必须至少是输入迭代器。

用 == 运算符来比较两个序列的第一个版本期望 3 个输入迭代器参数，前两个参数是第一个序列的开始和结束迭代器，第三个参数是第二个序列的开始迭代器。如果第二个序列中包含的元素少于第一个序列，结果是未定义的。用 == 运算符的第二个版本期望 4 个参数：第一个序列的开始和结束迭代器，第二个序列的开始和结束迭代器，如果两个序列的长度不同，那么结果总是为 false。本节会演示这两个版本，但推荐使用接受 4 个参数的版本，因为它不会产生未定义的行为。

下面是一个演示如何应用它们的示例：

```
// Using the equal() algorithm
#include <iostream>                                      // For standard streams
#include <vector>                                        // For vector container
#include <algorithm>                                     // For equal() algorithm
#include <iterator>                                      // For stream iterators
#include <string>                                        // For string class
using std::string;

int main()
{
    std::vector<string> words1 {"one", "two", "three", "four", "five", "six", "seven", "eight", "nine"};
    std::vector<string> words2 {"two", "three", "four", "five", "six", "seven", "eight", "nine", "ten"};
    auto iter1 = std::begin(words1);
    auto end_iter1 = std::end(words1);
    auto iter2 = std::begin(words2);
    auto end_iter2 = std::end(words2);

    std::cout << "Container - words1:";
    std::copy(iter1, end_iter1, std::ostream_iterator<string>{std::cout, " "});
    std::cout << "\nContainer - words2:";
    std::copy(iter2, end_iter2, std::ostream_iterator<string>{std::cout, " "});
    std::cout << std::endl;

    std::cout << "\n1\. Compare from words1[1] to end with words2:";
    std::cout << std::boolalpha << std::equal(iter1 + 1, end_iter1, iter2) << std::endl;

    std::cout << "2\. Compare from words2[0] to second-to-last with words1:";
    std::cout << std::boolalpha << std::equal(iter2, end_iter2 - 1, iter1) << std::endl;

    std::cout << "3\. Compare from words1[1] to words1[5] with words2:";
    std::cout << std::boolalpha << std::equal(iter1 + 1, iter1 + 6, iter2) << std::endl;

    std::cout << "4\. Compare first 6 from words1 with first 6 in words2:";
    std::cout << std::boolalpha << std::equal(iter1, iter1 + 6, iter2, iter2 + 6) << std::endl;

    std::cout << "5\. Compare all words1 with words2:";
    std::cout << std::boolalpha << std::equal(iter1, end_iter1, iter2) << std::endl;

    std::cout << "6\. Compare all of words1 with all of words2:";
    std::cout << std::boolalpha << std::equal(iter1, end_iter1, iter2, end_iter2) << std::endl;

    std::cout << "7\. Compare from words1[1] to end with words2 from first to second-to-last:";
    std::cout << std::boolalpha << std::equal(iter1 + 1, end_iter1, iter2, end_iter2 - 1) << std::endl;
}
```

输出结果为：

Container - words1: one two three four five six seven eight nine
Container - words2: two three four five six seven eight nine ten
1.Compare from words1[1] to end with words2: true
2.Compare from words2[0] to second-to-last with words1: false
3.Compare from words1[1] to wordsl[5] with words2: true
4.Compare first 6 from words1 with first 6 in words2: false
5.Compare all wordsl with words2: false
6.Compare all of words1 with all of words2: false
7.Compare from words1[1] to end with words2 from first to second_to_last: true

在这个示例中，对来自于 words1 和 words2 容器的元素的不同序列进行了比较。equal() 调用产生这些输出的原因如下：

*   第 1 条语句的输出为 true，因为 words1 的第二个元素到最后一个元素都从 words2 的第一个元素开始匹配。第二个序列的元素个数比第一个序列的元素个数多 1，但 第一个序列的元素个数决定了比较多少个对应的元素。
*   第 2 条语句的输出为 false，因为有直接的不匹配；words2 和 words1 的第一个元素不同。
*   第 3 条语句的输出为 true，因为 word1 中从第二个元素开始的 5 个元素和 words2 的前五个元素相等。
*   在第 4 条语句中，words2 的元素序列是由开始和结束迭代器指定的。序列长度相同，但它们的第一个元素不同，所以结果为 false。
*   在第 5 条语句中，两个序列的第一个元素直接就不匹配，所以结果为 false。
*   第 6 条语句的输出为 false，因为序列是不同的。这条语句不同于前面的 equal() 调用，因为指定了第二个序列的结束迭代器。
*   第 7 条语句会从 words1 的第二个元素开始，与 word2 从第一个元素开始比较相同个数的元素，所以输出为 true。

当用 equal() 从开始迭代器开始比较两个序列时，第二个序列用来和第一个序列比较的元素个数由第一个序列的长度决定。就算第二个序列比第一个序列的元素多，equal() 仍然会返回 true。如果为两个序列提供了开始和结束迭代器，为了使结果为 true，序列必须是相同的长度。

尽管可以用 equal() 来比较两个同种类型的容器的全部内容，但最好还是使用容器的成员函数 operator==() 来做这些事。示例中的第 6 条输出语句可以这样写：

```
std::cout << std::boolalpha << (words1 == words2) << " "; // false
```

这两个版本的 equal() 接受一个谓词作为额外的参数。这个谓词定义了元素之间的等价 比较。下面是一个说明它们用法的代码段：

```
std::vector<string> r1 { "three", "two", "ten"};
std::vector<string> r2 {"twelve", "ten", "twenty" };
std::cout << std::boolalpha<< std::equal (std::begin (r1) , std::end (r1) , std::begin (r2),[](const string& s1, const string& s2) { return s1[0] = s2[0]; })<< std::endl; // true
std::cout << std::boolalpha<<std::equal(std::begin(r1), std::end(r1), std::begin(r2), std::end(r2),[](const string& s1, const string& s2) { return s1[0] == s2[0]; }) << std::endl; // true
```

在 equal() 的第一次使用中，第二个序列是由开始迭代器指定的。谓词是一个在字符串 参数的第一个字符相等时返回 true 的 lambda 表达式。最后一条语句表明，equal() 算法可以使用两个全范围的序列，并使用相同的谓词。

不应该用 equal() 来比较来自于无序 map 或 set 容器中的元素序列。在无序容器中，一组给定元素的顺序可能和保存在另一个无序容器中的一组相等元素不同，因为不同容器的元素很可能会被分配到不同的格子中。