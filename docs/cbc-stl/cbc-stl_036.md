# C++ list（STL list）访问（获取）元素方法详解

list 的成员函数 front() 和 back()，可以各自返回第一个和最后一个元素的引用。在空 list 中调用它们中的任意一个，结果是未知的，因此不要这样使用。可以通过迭代器的自增或自减来访问 list 的内部元素。

正如我们所了解的那样，begin() 和 end() 分别返回的是指向第一个和最后一个元素下一个位置的双向迭代器。rbegin() 和 rend() 函数返回的双向迭代器，可以让我们逆序遍历元素。因为可以对 list 使用基于范围的循环，所以当我们想要处理所有元素时，可以不使用迭代器：

```
std::list<std::string> names {"Jane","Jim", "Jules", "Janet"};
names.emplace_back("Ann");
std::string name("Alan");
names.emplace_back(std::move(name)); names.emplace_front("Hugo");
names.emplace(++begin(names), "Hannah");
for(const auto& name : names)
    std::cout << name << std::endl;
```

循环变量 name 是依次指向每个 list 元素的引用，使得循环能够逐行输出各个字符串。下面通过一个练习检验一下前面学到的知识。这个练习读取通过键盘输入的读语并将它们存储到一个 list 容器中：

```
// Working with a list
#include <iostream>
#include <list>
#include <string>
#include <functional>

using std::list;
using std::string;

// List a range of elements
template<typename Iter>
void list_elements(Iter begin, Iter end)
{
    while (begin != end)
        std::cout << *begin++ << std::endl;
}

int main()
{
    std::list<string> proverbs;

    // Read the proverbs
    std::cout << "Enter a few proverbs and enter an empty line to end:" << std::endl;
    string proverb;
    while (getline(std::cin, proverb, '\n'), !proverb.empty())
        proverbs.push_front(proverb);

    std::cout << "Go on, just one more:" << std::endl;
    getline(std::cin, proverb, '\n');
    proverbs.emplace_back(proverb);

    std::cout << "The elements in the list in reverse order are:" << std::endl;
    list_elements(std::rbegin(proverbs), std::rend(proverbs));

    proverbs.sort(); // Sort the proverbs in ascending sequence
    std::cout << "\nYour proverbs in ascending sequence are:" << std::endl;
    list_elements(std::begin(proverbs), std::end(proverbs));

    proverbs.sort(std::greater<>()); // Sort the proverbs in descending sequence
    std::cout << "\nYour proverbs in descending sequence:" << std::endl;
    list_elements(std::begin(proverbs), std::end(proverbs));
}
```

运行结果为：

Enter a few proverbs and enter an empty line to end: A nod is a good as a wink to a blind horse.
Many a mickle makes a muckle.
A wise man stands on the hole in his carpet.
Least said, soonest mended.

Go on, just one more:
A rolling stone gathers no moss.
The elements in the list in reverse order are:
A rolling stone gathers no moss.
A nod is a good as a wink to a blind horse.
Many a mickle makes a muckle.
A wise man stands on the hole in his carpet.
Least said, soonest mended.

Your proverbs in ascending sequence are:
A nod is a good as a wink to a blind horse.
A rolling stone gathers no moss.
A wise man stands on the hole in his carpet.
Least said, soonest mended.
Many a mickle makes a muckle.

Your proverbs in descending sequence:
Many a mickle makes a muckle.
Least said, soonest mended.
A wise man stands on the hole in his carpet.
A rolling stone gathers no moss.
A nod is a good as a wink to a blind horse.

因为输入的一系列谚语中包含空格，所以可以使用 getline() 函数来输入。每个谚语都是单行读入，然后通过调用 push_front() 函数，将它们作为新的 list 元素添加到 proverbs 容器中。这里，为了练习使用 emplace_back()，额外添加了一个谚语。由定义在 main() 前面的函数模板 list_dements() 产生输出结果。这个函数模板可以输出任意类型的支持流插入操作的元素，只要元素的容器提供输出迭代器。上面的代码演示了如何将这个函数模板与前向迭代器和反向迭代器一起使用。

第一次调用 proverbs 的成员函数 sort() 时，没有提供参数，因此元素被默认排成升序。第二次调用时，提供了一个 greater 断言作为参数；这个模板和其他几个会在后面遇到的标准断言模板都定义在头文件 functional 中。表达式 greater<>() 定义了一个函数对象，这个函数对象可以用 opemtor>() 来比较对象，推导模板参数类型。结果，list 中的元素变成了降序排列。还有其他一些对象，它们也定义了一些 sort() 会经常用到的断言，包括 greater_equal<>()、less<>() 和 less_equal<>()。从名称就可以看出它们是如何进行比较的。从这个示例的输入来看，一切都很符合我们的预期。