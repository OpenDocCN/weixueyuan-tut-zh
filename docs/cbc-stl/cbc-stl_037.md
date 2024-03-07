# C++ forward_list 用法详解

forward_list 容器以单链表的形式存储元素。forward_list 的模板定义在头文件 forward_list 中。fdrward_list 和 list 最主要的区别是:它不能反向遍历元素；只能从头到尾遍历。

forward_list 的单向链接性也意味着它会有一些其他的特性：

1.  无法使用反向迭代器。只能从它得到 const 或 non-const 前向迭代器，这些迭代器都不能解引用，只能自增；
2.  没有可以返回最后一个元素引用的成员函数 back();只有成员函数 front();
3.  因为只能通过自增前面元素的迭代器来到达序列的终点，所以 push_back()、pop_back()、emplace_back()也无法使用。

forward_list 的操作比 list 容器还要快，而且占用的内存更少，尽管它在使用上有很多限制，但仅这一点也足以让我们满意了。

forward_list 容器的构造函数的使用方式和 list 容器相同。forward_list 的迭代器都是前向迭代器。它没有成员函数 size()，因此不能用一个前向迭代器减去另一个前向迭代器，但是可以通过使用定义在头文件 iterator 中的 distance() 函数来得到元素的个数。例如：

```
std::forward_list<std::string> my_words {"three", "six", "eight"};
auto count = std::distance(std::begin(my_words),std::end(my_words));
// Result is 3
```

distance() 的第一个参数是一个开始迭代器，第二个参数是一个结束迭代器，它们指定了元素范围。当需要将前向迭代器移动多个位置时，advance() 就派上了用场。例如：

```
std::forward_list<int> data {10, 21, 43, 87, 175, 351};
auto iter = std::begin(data);
size_t n {3};
std::advance(iter, n);
std::cout << "The " << n+1 << "th element is n << *iter << std::endl;
// Outputs 87
```

这并不神奇。advance() 函数会将前向迭代器自增需要的次数。这使我们不必去循环自增迭代器。需要记住的是这个函数自增的是作为第一个参数的迭代器，但是并不会返回它——advance() 的返回类型为 void。

因为 forward_list 正向链接元素，所以只能在元素的后面插入或粘接另一个容器的元素，这一点和 list 容器的操作不同，list 可以在元素前进行操作。因为这个，forward_list 包含成员函数 splice_after() 和 insert_after()，用来代替 list 容器的 splice() 和 insert()；顾名思义，元素会被粘接或插入到 list 中的一个特定位置。当需要在 forward_list 的开始处粘接或插入元素时，这些操作仍然会有问题。除了第一个元素，不能将它们粘接或插入到任何其他元素之前。

这个问题可以通过使用成员函数 cbefore_begin() 和 before_begin() 来解决。它们分别可以返回指向第一个元素之前位置的 const 和 non-const 迭代器。所以可以使用它们在开始位置插入或粘接元素。例如：

```
std::forward_list<std::string> my_words {"three", "six", "eight"}
std::forward_list<std::string> your_words {"seven", "four", "nine"};
my_words.splice_after(my_words.before_begin(), your_words, ++std::begin(your_words));
```

这个操作的效果是将 your_words 的最后一个元素粘接到 my_words 的开始位置，因此 my_words 会包含这些字符串对象:"ninef"、"three"、"six"、"eight"。这时 your_words 就只剩下两个字符串元素："seven"和"four”。

还有另一个版本的 splice_afler()，它可以将 forward_list<T> 的一段元素粘接到另一个容器中：

```
my_words.splice_after (my_words . before_begin () , your_words,++std::begin(your_words), std::end(your_words));
```

最后两个迭代器参数，指定了第三个参数所指定的 fbrward_list<T> 容器的元素范围。在这个范围内的元素，除了第一个，其他的都被移到第一个参数所指定容器的特定位置。 因此，如果在容器初始化后执行这条语句，my_words 会包含"four"、"nine"、"three"、"six"、 "eight"，your_words 仅仅包含"seven”。

另一个版本的 splice_after() 会将一个 forward_list<T> 容器的全部元素粘接到另一个容器中：

```
my_words.splice_after(my_words.before_begin(), your_words);
```

上面的代码会将 your_words 中的全部元素拼接到第一个元素指定的位置。

forward_list 和 list —样都有成员函数 sort() 和 merge()，它们也都有 remove()、remove_if() 和 unique()，所有这些函数的用法都和 list 相同。我们可以尝试在一个示例中演示 forward_list 的操作。容器会包含一些代表矩形盒子的 Box 类对象。下面是 Box 类的头文件中的内容：

```
// Defines the Box class for Ex2_06
#ifndef BOX_H
#define BOX_H
#include <iostream>                              // For standard streams
#include <utility>                               // For comparison operator templates
using namespace std::rel_ops;                    // Comparison operator template namespace

class Box
{
private:
    size_t length {};
    size_t width {};
    size_t height {};

public:
    explicit Box(size_t l = 1, size_t w = 1, size_t h = 1) : length {l}, width {w}, height {h} {}
    double volume() const { return length*width*height; }
    bool operator<(const Box& box) { return volume() < box.volume(); }
    bool operator==(const Box& box) { return length == box.length&& width == box.width&&height == box.height; }

    friend std::istream& operator>>(std::istream& in, Box& box);
    friend std::ostream& operator<<(std::ostream& out, const Box& box);
};

inline std::istream& operator>>(std::istream& in, Box& box)
{
    std::cout << "Enter box length, width, & height separated by spaces - Ctrl+Z to end: ";
    size_t value;
    in >> value;
    if (in.eof()) return in;

    box.length = value;
    in >> value;
    box.width = value;
    in >> value;
    box.height = value;
    return in;
}

inline std::ostream& operator<<(std::ostream& out, const Box& box)
{
    out << "Box(" << box.length << "," << box.width << "," << box.height << ")  ";
    return out;
}
#endif
```

utility 头文件中的命名空间 std::relops 包含一些比较运算符的模板。如果一个类已经定义了 operator<() 和 operator==()，那么在需要时，这个模板会生成剩下的比较运算符函数。

Box 类有三个私有成员，它们定义了 Box 对象的整体尺寸。带默认值的构造函数提供了一个无参构造函数，当在容器中保存 Box 对象时，需要使用它；没有初始化的元素由这种元素默认的构造函数生成。内联的友元函数重载了流的提取和插入运算，这显然包含标准输入输出流。

每次以三个输入值为一组，在读入第一个输入值后，operator>>() 函数会通过调用流对象的 eof() 函数来检查是否读到 EOF。当输入 Ctrl+Z 或从文件输入流中读到文件结束符时，标准输入流会被设置为 EOF。当这些发生时会结束输入，然后返回一个流对象，EOF 状态会继续保持，因此调用程序可以检测到这个状态。

下面是一个在 forward_list 容器中保存 Box 对象的示例：

```
// Working with a forward list
#include <algorithm> // For copy()
#include <iostream> // For standard streams
#include <forward_list> // For forward_list container
#include <iterator> // For stream iterators
#include "Box.h"
// List a range of elements
template<typename Iter>
void list_elements(Iter begin, Iter end)
{
    size_t perline {6};                            // Maximum items per line
    size_t count {};                               // Item count
    while (begin != end)
    {
        std::cout << *begin++;
        if (++count % perline == 0)
        {
            std::cout << "\n";
        }
    }
    std::cout << std::endl;
}

int main()
{
    std::forward_list<Box> boxes;
    std::copy(std::istream_iterator<Box>(std::cin), std::istream_iterator<Box>(), std::front_inserter(boxes));

    boxes.sort();                                    // Sort the boxes
    std::cout << "\nAfter sorting the sequence is:\n";
    // Just to show that we can with Box objects - use an ostream iterator
    std::copy(std::begin(boxes), std::end(boxes), std::ostream_iterator<Box>(std::cout, " "));
    std::cout << std::endl;

    // Insert more boxes
    std::forward_list<Box> more_boxes {Box {3, 3, 3}, Box {5, 5, 5}, Box {4, 4, 4}, Box {2, 2, 2}};
    boxes.insert_after(boxes.before_begin(), std::begin(more_boxes), std::end(more_boxes));
    std::cout << "After inserting more boxes the sequence is:\n";
    list_elements(std::begin(boxes), std::end(boxes));

    boxes.sort();                                    // Sort the boxes
    std::cout << std::endl;
    std::cout << "The sorted sequence is now:\n";
    list_elements(std::begin(boxes), std::end(boxes));

    more_boxes.sort();
    boxes.merge(more_boxes);                         // Merge more_boxes
    std::cout << "After merging more_boxes the sequence is:\n";
    list_elements(std::begin(boxes), std::end(boxes));

    boxes.unique();
    std::cout << "After removing successive duplicates the sequence is:\n";
    list_elements(std::begin(boxes), std::end(boxes));

    // Eliminate the small ones
    const double max_v {30.0};
    boxes.remove_if(max_v{ return box.volume() < max_v; });
    std::cout << "After removing those with volume less than 30 the sorted sequence is:\n";
    list_elements(std::begin(boxes), std::end(boxes));
}
```

运行结果为：

Enter box length, width, & height separated by spaces - Ctrl+Z to end: 4 4 5
Enter box length, width, & height separated by spaces - Ctrl+Z to end: 6 5 7
Enter box length, width, & height separated by spaces - Ctrl+Z to end: 2 2 3
Enter box length, width, & height separated by spaces - Ctrl+Z to end: 12 3
Enter box length, width, & height separated by spaces - Ctrl+Z to end: 3 3 4
Enter box length, width, & height separated by spaces - Ctrl+Z to end: 3 3 3
Enter box length, width, & height separated by spaces - Ctrl+Z to end: ^Z
After sorting the sequence is:
Box(l,2,3) Box(2,2,3) Box(3,3,3) Box(3,3,4) Box(4,4,5) Box(6,5,7)
After inserting more boxes the sequence is:
Box(3,3,3) Box(5,5,5) Box(4,4,4) Box{2,2,2) Box(1,2,3) Box(2,2,3)
Box(3,3,3) Box(3,3,4) Box(4,4,5) Box(6,5,7)

The sorted sequence is now:
Box(l,2,3) Box(2,2,2) Box(2,2,3) Box(3,3,3) Box(3,3,3) Box(3,3,4)
Box (4,4,4) Box(4,4,5) Box(5,5,5) Box(6,5,7)
After merging more_boxes the sequence is:
Box(1,2,3) Box(2,2,2) Box(2,2,2) Box(2,2,3) Box(3,3,3) Box(3,3,3)
Box(3,3,3) Box(3,3,4) Box(4,4⁴) Box(4,4,4) Box(4,4,5) Box(5,5,5)
Box(5,5,5) Box(6,5,7)
After removing successive duplicates the sequence is:
Box(1,2,3) Box(2,2,2) Box(2,2,3) Box (3,3,3) Box(3,3,4) Box(4,4,4)
Box(4,4,5) Box(5,5,5) Box(6,5,7)
After removing those with volume less than 30 the sorted sequence is:
Box(3,3,4) Box(4,4) Box(4,4,5) Box(5,5,5) Box(6,5,7)

函数模板 list_dements() 用来输出由开始和结束迭代器指定的元素，每 6 个元素一行。这里用来输出 main() 中的 forward_list 的内容。在 main() 中，首先使用 copy() 算法，以 istream_iterator<Box> 对象作为数据源，以 front_inserter 作为数据存放地，从 cin 中读入一些 Box 对象的尺寸。istream_iterator 会调用定义在 Box.h 中的函数 operator>>() 来读取 Box 对象。

front_inserter 会调用容器的成员函数 push_front()，这是为 forward_list 所做的工作。对 boxes 容器中的元素进行排序后，我们会通过 copy() 算法将元素转移到 ostream_iterator<Box> 对象中，然后输出它们。这个迭代器会调用定义在 Box.h 中的函数 operator<<()。这里有一个限制，我们无法控制每行元素输出的个数。剩下的代码是用模板 list_elements() 输出的实例。

另一个 forward_list 容器 more_boxes 的内容被插入到 boxes 容器的头部。这是通过以函数 before_begin() 返回的迭代器作为插入位置，然后调用函数 insert_after() 来实现的。

下一步操作是对 boxes 中的元素进行排序，然后将 more_boxes 的内容合并到 boxes 中。这两个容器在调用 merge()前都必须先排序，因为只有在两个容器都是升序时，这个操作才能正常进行。因为插入新元素，显然会使 boxes 中出现一些重复的元素。调用 boxes 的函数 unique() 就可以消除连续重复的元素。

最后一个操作是调用 boxes() 的函数 remove_if() 来 删除容器中的一些元素。由作为参数传入的一元断言来决定删除哪些元素。这里使用的是一个 lambda 表达式，如果元素体积小于 max_v，就返回 true。max_v 是从外部区域以值的方式捕获的，因此外部区域和表达式内部的值可能不同。由输出可以看出，所有的操作都符合预期。