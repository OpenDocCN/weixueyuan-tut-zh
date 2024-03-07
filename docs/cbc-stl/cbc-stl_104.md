# C++ mismatch(STL mismatch)算法详解

equal() 算法可以告诉我们两个序列是否匹配。mismatch() 算法也可以告诉我们两个序列是否匹配，而且如果不匹配，它还能告诉我们不匹配的位置。

mismatch() 的 4 个版本和 equal() 一样有相同的参数——第二个序列有或没有结束迭代器，有或没有定义比较的额外的函数对象参数。mismatch() 返回的 pair 对象包含两个迭代器。它的 first 成员是一个来自前两个参数所指定序列的迭代器，second 是来自于第二个序列的迭代器。当序列不匹配时，pair 包含的迭代器指向第一对不匹配的元素；因此这个 pair 对象为 pair<iter1+n，iter2 + n>，这两个序列中索引为 n 的元素是第一个不匹配的元素。

当序列匹配时，pair 的成员取决于使用的 mismatch() 的版本和具体情况。iter1 和 end_iter1 表示定义第一个序列的迭代器，iter2 和 end_iter2 表示第二个序列的开始和结束迭代器。返回的匹配序列的 pair 的内容如下：

对于 mismatch(iter1，end_iter1，iter2):

*   返回 pair<end_iter1，(iter2 + (end_ter1 - iter1))>，pair 的成员 second 等于 iter2 加上第一个序列的长度。如果第二个序列比第一个序列短，结果是未定义的。

对于 mismatch(iterl, end_iter1, iter2, end_iter2)：

*   当第一个序列比第二个序列长时，返回 pair<end_iter1, (iter2 + (end_iter1 - iter1))>，所以成员 second 为 iter2 加上第一个序列的长度。
*   当第二个序列比第一个序列长时，返回 pair<(iter1 + (end_iter2 - iter2)),end_iter2>， 所以成员 first 等于 iter1 加上第二个序列的长度。
*   当序列的长度相等时，返回 pair<end_iter1, end_iter2>。

不管是否添加一个用于比较的函数对象作为参数，上面的情况都同样适用。

下面是一个使用带有默认相等比较的 mismatch() 的示例：

```
// Using the mismatch() algorithm
#include <iostream>                                      // For standard streams
#include <vector>                                        // For vector container
#include <algorithm>                                     // For equal() algorithm
#include <string>                                        // For string class
#include <iterator>                                      // For stream iterators

using std::string;
using word_iter = std::vector<string>::iterator;

int main()
{
    std::vector<string> words1 {"one", "two", "three", "four", "five", "six", "seven", "eight", "nine"};
    std::vector<string> words2 {"two", "three", "four", "five", "six", "eleven", "eight", "nine", "ten"};
    auto iter1 = std::begin(words1);
    auto end_iter1 = std::end(words1);
    auto iter2 = std::begin(words2);
    auto end_iter2 = std::end(words2);

    // Lambda expression to output mismatch() result
    auto print_match = [](const std::pair<word_iter, word_iter>& pr, const word_iter& end_iter)
    {
        if(pr.first != end_iter)
            std::cout << "\nFirst pair of words that differ are "<< *pr.first << " and " << *pr.second << std::endl;
        else
            std::cout << "\nRanges are identical." << std::endl;
    };

    std::cout << "Container - words1:  ";
    std::copy(iter1, end_iter1, std::ostream_iterator<string>{std::cout, " "});
    std::cout << "\nContainer - words2:  ";
    std::copy(iter2, end_iter2, std::ostream_iterator<string>{std::cout, " "});
    std::cout << std::endl;

    std::cout << "\nCompare from words1[1] to end with words2:";
    print_match(std::mismatch(iter1 + 1, end_iter1, iter2), end_iter1);

    std::cout << "\nCompare from words2[0] to second-to-last with words1:";
    print_match(std::mismatch(iter2, end_iter2 - 1, iter1), end_iter2 - 1);

    std::cout << "\nCompare from words1[1] to words1[5] with words2:";
    print_match(std::mismatch(iter1 + 1, iter1 + 6, iter2), iter1 + 6);

    std::cout << "\nCompare first 6 from words1 with first 6 in words2:";
    print_match(std::mismatch(iter1, iter1 + 6, iter2, iter2 + 6), iter1 + 6);

    std::cout << "\nCompare all words1 with words2:";
    print_match(std::mismatch(iter1, end_iter1, iter2), end_iter1);

    std::cout << "\nCompare all of words2 with all of words1:";
    print_match(std::mismatch(iter2, end_iter2, iter1, end_iter1), end_iter2);

    std::cout << "\nCompare from words1[1] to end with words2[0] to second-to-last:";
    print_match(std::mismatch(iter1 + 1, end_iter1, iter2, end_iter2 - 1), end_iter1);
}
```

注意 words2 中的内容和前面示例中的有些不同。每一次应用 mismatch() 的结果都是由定义为 print_match 的 lambda 表达式生成的。它的参数是一个 pair 对象和一个 vector<string> 容器的迭代器。使用 using 指令生成 word_iter 别名可以使 lambda 表达式的定义更简单。

在 main() 的代码中使用了不同版本的 mismatch()，它们都没有包含比较函数对象的参数。如果第二个序列只用开始迭代器指定，为了和第一个序列匹配，它只需要有和第一个序列相等长度的元素，但也可以更长。如果第二个序列是完全指定的，会由最短的序列来确定比较多少个元素。

输出如下：

Container - words1: one two three four five six seven eight nine
Container - words2: two three four five six eleven eight nine ten

Compare from words1[1] to end with words2:
First pair of words that differ are seven and eleven

Compare from words2[0] to second-to-last with words1:
First pair of words that differ are two and one

Compare from words1[1] to words1[5] with words2:
Ranges are identical.

Compare first 6 from words1 with first 6 in words2:
First pair of words that differ are one and two

Compare all words1 with words2:
First pair of words that differ are one and two

Compare all of words2 with all of words1:
First pair of words that differ are two and one

Compare from words1[1] to end with words2[0] to second-to-last:
First pair of words that differ are seven and eleven

输出显示了每个 mismatch() 的运用结果。 在我们提供自己的函数对象时，就可以完全灵活地定义相等比较。例如：

```
std::vector<string> range1 {"one", "three", "five", "ten"};
std::vector<string> range2 {"nine", "five", "eighteen”，"seven"};
auto pr = std::mismatch( std::begin(range1), std::end(range1),std:rbegin(range2), std::end(range2),[](const string& s1, const string& s2) { return s1.back() = s2.back(); });
if(pr.first == std::end(range1) || pr.second == std::end(range2))
    std::cout << "The ranges are identical." << std::endl;
else
    std::cout << *pr.first << " is not equal to " << *pr.second <<std::endl;
```

当两个字符串的最后一个字符相等时，这个比较会返回 true，所以这段代码的输出为：

five is not equal to eighteen

当然，这是正确的，而且根据比较函数，“one”等于“nine”，“three”等于“five”。