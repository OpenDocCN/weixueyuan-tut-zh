# C++ next_permutation(STL next_permutation)算法详解

排列就是一次对对象序列或值序列的重新排列。例如，“ABC”中字符可能的排列是：

"ABC", "ACB", "BAC", "BCA", "CAB", "CBA"

三个不同的字符有 6 种排列，这个数字是从 3*2*1 得到的。一般来说，n 个不同的字 符有 n! 种排列，n! 是 nx(n_1)x(n-2)...x2x1。很容易明白为什么要这样算。有 n 个对象 时，在序列的第一个位置就有 n 种可能的选择。对于第一个对象的每一种选择，序列的第 二个位置还剩下 n-1 种选择，因此前两个有 nx((n-1) 种可能选择。在选择了前两个之后， 第三个位置还剩下 n-2 种选择，因此前三个有 nx(n-1)x(n-2) 种可能选择，以此类推。序列的末尾是 Hobson 选择，因为只剩下 1 种选择。

对于包含相同元素的序列来说，只要一个序列中的元素顺序不同，就是一种排列。next_permutation() 会生成一个序列的重排列，它是所有可能的字典序中的下一个排列，默认使用 < 运算符来做这些事情。它的参数为定义序列的迭代器和一个返回布尔值的函数，这个函数在下一个排列大于上一个排列时返回 true，如果上一个排列是序列中最大的，它返回 false，所以会生成字典序最小的排列。

下面展示了如何生成一个包含 4 个整数的 vector 的排列：

```
std::vector<int> range {1,2,3,4};
do {
    std::copy (std::begin(range), std::end(range), std::ostream_iterator<int>{std::cout, " "});
    std::cout << std::endl;
}while(std::next_permutation(std::begin(range), std::end(range)));
```

当 next_permutation() 返回 false 时，循环结束，表明到达最小排列。这样恰好可以生成 序列的全部排列，这只是因为序列的初始排列为 1、2、3、4，这是排列集合中的第一个排列。有一种方法可以得到序列的全排列，就是使用 next_permutation() 得到的最小排列：

```
std::vector<string> words { "one", "two", "three", "four", "five", "six", "seven", "eight"};
while(std::next_permutation(std::begin(words)， std::end(words)));
do
{
    std::copy(std::begin(words), std::end(words), std::ostream_iterator<string>{std::cout, " "});
    std::cout << std::endl;
} while(std::next_permutation(std::begin(words), std::end(words)));
```

words 中的初始序列不是最小的排列序列，循环会继续进行，直到 words 包含最小排列。do-wliile 循环会输出全部的排列。如果想执行这段代码，需要记住它会生成 8! 种排列，从而输出 40320 行，因此首先可能会减少 words 中元素的个数。

当排列中的每个元素都小于或等于它后面的元素时，它就是元素序列的最小排列，所以可以用 min_element() 来返回一个指向序列中最小元素的迭代器，然后用 iter_swap() 算法交换两个迭代器指向的元素，从而生成最小的排列，例如：

```
std::vector<string> words { "one", "two", "three", "four", "five","six",
"seven", "eight"};
for (auto iter = std::begin(words); iter != std::end(words)-1 ;++iter)
    std::iter_swap(iter, std::min_element(iter, std::end(words)));
```

for 循环从序列的第一个迭代器开始遍历，直到倒数第二个迭代器。for 循环体中的语句会交换 iter 指向的元素和 min_element() 返回的迭代器所指向的元素。这样最终会生成一个最小排列，然后可以用它作为 next_permutation() 的起始点来生成全排列。

在开始生成全排列之前，可以先生成一个原始容器的副本，然后在循环中改变它，从 而避免到达最小排列的全部开销。

```
std::vector<string> words {"one","two", "three", "four", "five", "six", "seven", "eight"};
auto words_copy = words; // Copy the original
do {
    std::copy(std::begin(words), std::end(words), std::ostream_iterator<string>{std::cout, " "});
    std::cout << std::endl;
    std::next_permutation(std::begin(words), std::end(words));
}while(words != words_copy); // Continue until back to the original
```

循环现在会继续生成新的排列，直到到达原始排列。下面是一个找出单词中字母的全部排列的示例：

```
// Finding rearrangements of the letters in a word
#include <iostream>                                      // For standard streams
#include <iterator>                                      // For iterators and begin() and end()
#include <string>                                        // For string class
#include <vector>                                        // For vector container
#include <algorithm>                                     // For next_permutation()
using std::string;

int main()
{
    std::vector<string> words;
    string word;
    while(true)
    {
        std::cout << "\nEnter a word, or Ctrl+z to end: ";
        if((std::cin >> word).eof()) break;
        string word_copy {word};
        do
        {
            words.push_back(word);
            std::next_permutation(std::begin(word), std::end(word));
        } while(word != word_copy);

        size_t count{}, max{8};
        for(const auto& wrd : words)
            std::cout << wrd << ((++count % max == 0) ? '\n' : ' ');
        std::cout << std::endl;
        words.clear();                     // Remove previous permutations
    }
}
```

这段代码会从标准输入流读取一个单词到 word 中，然后在 word_copy 中生成一个副本，将 word 中字符的全排列保存到 words 容器中。这个程序会继续处理单词直到按下 Ctrl+Z 组合键。用 word 的副本来判断是否已经保存了全排列。然后所有的排列会被写入输出流，8 个一行。像之前说的那样，随着被排列元素个数的增加，排列的个数增加也很快，所以这里不要尝试使用太长的单词。

可以为 next_permutation() 提供一个函数对象作为第三个参数，从而用这个函数对象定 义的比较函数来代替默认的比较函数。下面展示如何使用这个版本的函数，通过比较最后 一个字母的方式来生成 words 序列的排列：

```
std::vector<string> words { "one", "two", "four", "eight"};
do {
    std::copy(std:rbegin(words), std::end(words), std::ostream_iterator<string> {std::cout, " "});
    std::cout << std::endl;
} while(std::next_permutation(std::begin(words), std::end(words),[](const string& s1, const strings s2) {return s1.back() < s2.back(); }));
```

通过传入一个 lambda 表达式作为 next_permutation() 的最后一个参数，这段代码会生成 words 中元素的全部 24 种排列。