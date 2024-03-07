# C++自定义迭代器（STL 自定义迭代器)的实现详解

迭代器对于任何自定义的类序列都是一个强大的附加工具。它允许我们将算法运用到有自定义类元素的容器上。可能会出现一种情形，没有可以满足我们需要的标准 STL 容器，这时候就需要定义一个自己的容器。我们的容器类可能需要迭代器。通过深入理解什么样的类(定义了迭代器)才能被 STL 所接受，可以让我们了解到 STL 内部发生了些什么。

## STL 迭代器的要求

STL 对定义了迭代器的类类型有一些特定的要求。这是为了保证所有接受这种迭代器的算法都可以正常工作。算法不需要知道，也不在乎它所处理的元素来自何种容器，但是它们在意传给它们作为参数的迭代器的特性。不同的算法要求不同功能的迭代器。我们在前面章节看到过这几类迭代器：输入迭代器、输出迭代器、前向迭代器、双向迭代器和随机访问迭代器。我们总是可以在需要低级别迭代器的地方使用高级别迭代器。

定义算法的模板需要决定可传入迭代器的类别，用来验证所传入的迭代器的功能是否足够。知道迭代器参数的类别能为算法的应用提供潜在的优势，可以充分利用任何最少的额外功能让算法更加高效。

一般来说，必须用标准的方式确认迭代器的功能。不同类别的迭代器意味着需要为迭代器类定义不同的成员函数集。我们知道，迭代器类别具有功能叠加性，这当然也会反映到每种类别的成员函数集上。在探讨这些之前，先介绍一下函数模板如何使用迭代器。

## 使用 STL 迭代器存在的问题

定义一个参数中有迭代器的函数模板会产生一个问题，我们并不总是知道在函数模板中要用到哪些类型的迭代器。思考下面用迭代器作为参数的交换函数；模板类型参数指定了迭代器类型：

```
template <typename Iter> void my_swap(Iter a, Iter b)
{
    tmp = *a;//error -- variable tmp undeclared
    *a = *b;
    *b = tmp;
}
```

函数模板的实例用来交换迭代器参数所指向的两个对象：a 和 b。dmp 应该是什么类型我们没法知道，我们知道迭代器指向对象的类型却无计可施，因为直到类模板生成实例时，才能确定对象的类型。在不知道对象的类型时，如何定义变量？当然，这里可以使用 auto。在一些情况下，我们也想知道迭代器类型的值和类型差别。

有些其他的机制可以确定一个迭代器参数所指向值的类型。一种是，坚持要求每个使用 my_swap() 的迭代器都应该包含一个公共定义的类型别名，因为这样就可以确定迭代器所指向对象的类型。既然这样，就可以在迭代器类中使用 value_type 的别名来指定函数模板 my_swap() 中 tmp 的类型，如下所示：

```
template <typename Iter> void my_swap(Iter a, Iter b)
{
    typename Iter::value—type tmp = *a;
    *a = *b;
    *b = tmp;
}
```

因为 value_type 的别名定义在 Iter 类中，所以可以通过用类名限定 value_type 的方式引用它。这样定义了 value_type 别名的迭代器类就能在函数中正常使用。然而，算法既使用指针，也使用迭代器；如果 Iter 是普通类型的指针，例如 int*，甚至是 Box*，而 Box 是类型一这样可能就无法使用了。因为指针不是类，不能包含定义的别名，所以不能写成 int*::value_type 或 Box*::value_type。STL 用模板优雅地解决了这个问题和其他一些相关问题！

## 走进 STL

模板类型 iterator_traits 定义在头文件 iterator 中。这个模板为迭代器的类型特性定义了一套标准的类型别名，让算法既可以用迭代器，也可以用一般的指针。iterator_traits 模板的定义如下所示：

```
template<class Iterator>
struct iterator_traits
{
    typedef typename Iterator::difference_type difference_type;
    typedef typename Iterator::value—type value_type;
    typedef typename Iterator::pointer pointer;
    typedef typename 工 terator::reference reference;
    typedef typename Iterator::iterator_category iterator_category;
}；
```

相信你肯定记得，结构体和类在本质上是相同的，除了结构体的成员默认是 public，这个结构体模板中没有成员变量和成员函数。iterator_traits 模板的主体只包含类型别名的定义。这些别名以 Iterator 作为类型参数的模板。它在模板的类型别名——difference_type、value_type 等，以及用来生成迭代器模板实例的类型，与对应 Iterator 的类型参数之间定义了映射。

因此，对于一个实体类 Boggle，iterator_traits<Boggle> 实例定义 difference_type 作为 Boggle::difference_type 的别名，定义 value_type 作为 Boggle::valuejype 的别名，等等。

这帮我们有效地解决了不知道模板定义中类型是什么的问题。首先，假设定义了一个迭代器类型 MyItemtor，它包含具有下列类型别名的定义：

*   difference_type：两个 MyIterator 类型的迭代器之间差别值的类型。
*   value_type：MyIterator 类型的迭代器所指向值的类型。
*   pointer：Mylterator 类型的迭代器所表示的指针类型。
*   reference：来自于 *MyIterator 的引用类型。
*   iterator_category：前面介绍的迭代器类别的标签类类型：它们是 input_iterator_tag、output_iterator_tag、forward_iterator_tag、bidirectional_iterator_tag、random_access_iterator_tag。

一个满足 STL 要求的迭代器类必须全部定义这些别名。但是对于输出迭代器，除了 iterator_category，所有的别名都可以定义为 void。这是因为输出迭代器指向对象的目的地址而不是对象。这套迭代器提供了我们所想知道的关于迭代器的一切。

当以迭代器为参数定义函数模板时，可以在模板中使用 iterator_traits 模板定义的标准类型别名。因此类型 MyIterator 的迭代器代表的指针类型总是可以作为 std::iterator_traits<MyIterator>:: pointer 引用，因为它等同于 MyIterator::pointer。当需要指定一个 MyIterator 迭代器所指向值的类型时，可以写作 std::iterator_traits<MyIterator>::value_type，这将会被映射为 Mylterator::value_ type。我们用 my_swap() 模板中的 iterator_traits 模板类型别名来指定 tmp 的类型，例如：

```
template <typename Iter>
void my_swap(Iter a, Iter b)
{
    typename std::iterator_traits<Iter>::value_type tmp = *a;
    *a = *b;
    *b = tmp;
}
```

上述代码将 tmp 的类型指定为 iterator_traits 模板中的 value_type 别名。当用 Iter 模板参数实例化 my_swap() 模板时，tmp 的类型变为迭代器所指向的类型 Iter::value_type。

为了说清楚发生了什么，以及是如何解决这个问题的，让我们考虑一个 my_swap() 模板实例的具体情况。假设一个程序包含下面的代码：

```
std::vector<std::string> words {"one", "two", "three"};
my_swap(std::begin(words), std::begin(words)+1); //Swap first two elements
```

当编译器遇到 my_swap() 调用时，它会生成一个基于调用参数的函数模板实例。模板类型的迭代器是 iterator<std::string>。在 my_swap() 模板的主体中，编译器不得不处理 tmp 的定义，编译器知道模板的类型参数是 iterator<std::string>，因此在向模板添加了这个类型后，tmp 的定义变为：

```
typename std::iterator_traits<iterator<std::string> >::value_type tmp = *a;
```

tmp 的类型现在是一个 iterator_traits 模板实例的成员变量。为了弄清楚这意味着什么，编译器需要使用 my_swap() 函数中用来指定 tmp 类型的类型参数来实例化 iterator_traits 模板。下面是一个编译器将会生成的 iterator_traits 模板实例：

```
struct iterator—traits
{
    typedef typename iterator<std::string>::difference_type difference_type;
    typedef typename iterator<std::string>::value_type value_type;
    typedef typename iterator<std::string>::pointer pointer;
    typedef typename iterator<std::string>::reference reference;
    typedef typename iterator<std:: string>:: iterator_category iterator_category;
}；
```

从这里编译器可以确定 tmp 的类型为 iterator_traits<iterator<std::string>>::value_type，然而它也是 iterator<std::string>::value_type 的别名。就像所有的 STL 迭代器类型，iterator<std::string> 类型的定义是从 iterator 模板中生成的，并且会包含 value_type 的定义， 看起来像下面这样：

```
typedef std::string value_type;
```

现在编译器从 iterator_traits 实例中知道 itemtor_traits<itemtor<std::string>>::value_type 是 iterator<std::string>::value_type 的别名，并且从 itemtor<std::string> 类定义中知道 iterator<std::string>:: value_type 是 std::string 的别名。通过将别名转换为真实类型，编译器推断出 my_swap() 函数中 tmp 的定义是：

```
std::string tmp = *a;
```

有必要提醒自己模板不是代码一它是编译器用来生成代码的配方。iterator_traits 模板只包含类型别名，因此不会产生可执行代码。编译器在生成 C++ 代码的过程中，会用到它。被编译的代码中将不会有 iterator_traits 模板的踪迹；它的唯一用武之地是在生成 C++ 代码的过程中。

这里仍然遗留了一些有关指针的问题。iterator_traits 如何让算法像接受迭代器一样接受指针。iterator_traits 模板特化了类型 T* 和 const T* 的定义。例如，当模板类型参数是指针类型 T* 时，特化被定义为：

```
template<class T>
struct iterator_traits<T*>
{
    typedef ptrdiff_t   difference_type;
    typedef T   value_type;
    typedef T*  pointer;
    typedef T&  reference;
    typedef random_access_iterator_tag iterator_category;
}；
```

当模板类型参数是指针类型时，这定义了对应于别名的类型。T* 类型的指针 value_type 的别名总是为 T；如果将 Box* 类型的指针作为 my_swap() 的参数，那么 value_type 的别名是 Box，因此 tmp 也为这种类型。

随机访问迭代器类别所要求的全部操作都可以运用到指针上。因此对于指针，iterator_categor 的别名总是等同于 std::random_access_iterator_tag 类型。因而 iterators_traits 能否正常工作取决于模板类型参数是指针还是迭代器类类型。当模板类型参数是指针时，会选择使用 iterators_traits 针对指针的特例化模板；否则选择标准的模板定义。

## 使用迭代器模板

STL 定义了迭代器模板，用来帮助我们在自己的迭代器类中包含要求的类型别名。 iterator 是一个结构体模板，它定义了 5 个来自于 iterator_traits 模板的类型别名：

```
template<class Category, class T, class Difference = ptrdiff_t, class Pointer =T*,class Reference = T&> struct iterator
{
    typedef T value—type;
    typedef Difference difference_type;
    typedef Pointer pointer;
    typedef Reference reference;
    typedef Category iterator_category
}；
```

这个模板定义了 STL 对迭代器所要求的全部类型。例如，如果有一个未知类型的模板参数 Iter，当需要声明一个指针时，它指向一个迭代器解引用时提供的类型，这时可以写作 Iter::pointer。 iterator_category 的值必定是在前面章节介绍的类别标签类中的一个。

当定义一个表示迭代器的类时，可以使用以迭代器模板为基类生成的实例，这样会添加类需要的类型别名。例如：

```
class My_Iterator : public std::iterator<std::random_access_iterator_tag,int>
{
    // Members of the iterator class...
}；
```

还需要注意，需要为迭代器定义 STL 要求的全部类型。模板的第 1 个参数指定了作为完全随机访问迭代器的迭代器类型。第 2 个参数是迭代器所指向对象的类型。最后的 3 个参数是默认值，因此第 3 个参数和这两个迭代器的类型不同，是 ptrdiff_t。第 4 个参数是一个指向对象的指针类型，因此是 int*。最后一个模板参数指定了引用的类型，是 int&。当然，迭代器类型不做任何事，仍然需要定义类的全部成员。

## STL 迭代器成员函数的要求

STL 定义了一套需要迭代器类型支持并且依赖迭代器类别的成员函数。如果把它们编成组，显然很有用。第一组需要全部迭代器和一些所有迭代器类都需要的重要函数：默认构造函数、拷贝构造函数以及赋值运算符。根据经验，如果为迭代器类定义了其中任意一个函数，就需要定义一个显式的析构函数。该组中类型迭代器的全套函数是：

```
Iterator(); // Default constructor
Iterator (const Iterator& y) ; // Copy constructor
~Iterator (); // Destructor
Iterator& operator= (const Iterator& y) ;// Assignment operator
```

对于随机访问迭代器类，STL 需要一整套的关系运算符。事实上，可以通过使用 utility 标准库头文件中的函数模板来完成这些定义：

```
bool operator==(const Iterator& y) const;
bool operator<(const Iterator& y) const;
```

下面假定已经直接 #include 头文件 utility，并且直接使用命名空间 std::relops：

```
#include <utility>
using namespace std::rel_ops;
```

如果为类定义了 operator=() 和 operator<()，然后 std 中声明的命名空间 rel_ops 在必要时，就可以包含我们为 !=、>、>= 和 <= 生成的运算符函数的函数模板。因此直接用 using 激活 std::rel_ops，就可以显式保存这 4 个运算符函数。如果定义一个运算符函数，但是它已经被命名空间 std::rd_ops 模板生成，那么我们的实现的优先级高于模板生成的实现。函数 operator() 比较特别，叫作顺序关系，它对搜索和比较算法很重要。

函数 operator==() 可以检验两个容器对象的内容是否相同。这是一个有趣的方面，对于任意一对操作数 x 和 y，表达式 (x<y || y<x || x==y) 总为真，因为这个表达式的三部分中总有一部分为真。

事实上，不需要那样做，如果 x=y 为真，那么 x<y 和 y<x 都不可能为真。唯一可以确定的是两个相等元素没有什么不同。然而，如果 x!=y，就无法断定中的哪个为真。当表达式 (!(x<y)) &&(!(y<x)) 为真时，就可以说 x 和 y 不相等，也就意味着在排序时没有偏向。这种情况的一个常见示例是对字符串进行排序时，但是要忽略大小写。如果以大小写敏感为基准，字符串 "A123" 和 "al23" 是不等价的(第一个字母不相同)，它们不相同，也不相等。

迭代器的其他操作由它的类别决定。可以在前面章节看到每种迭代器的特定操作，而且因为迭代器的累加特性，随机访问迭代器可以支持全部操作。

让我们在示例中看一个简单迭代器类型的定义。我们定义一个类模板，用来表示一段数值类型值，也可以生成指定范围的开始和结束迭代器。这个迭代器也是模板类型，两个模板都定义在同一个头文件 Numeric_Range.h 中。下面是 Numeric_Range<T> 模板的定义：

```
template <typename T> class Numeric_Iterator;    // Template type declaration
// Defines a numeric range
template<typename T>
class Numeric_Range
{
    static_assert(std::is_integral<T>::value || std::is_floating_point<T>::value,
                                 "Numeric_Range type argument must be numeric.");
    friend class Numeric_Iterator < T >;
protected:
    T start;                                       // First value in the range
    T step;                                        // Increment between successive values
    size_t count;                                  // Number of values in the range
public:
    Numeric_Range(T first=0, T incr=1, size_t n=2) : start {first}, step {incr}, count {n}{}
    // Return the begin iterator for the range
    Numeric_Iterator<T> begin(){ return Numeric_Iterator<T>(*this); }
    // Return the end iterator for the range
    Numeric_Iterator<T> end()
    {
        Numeric_Iterator<T> end_iter(*this);
        end_iter.value = start + count*step;          // End iterator value is one step over the last
        return end_iter;
    }
};
```

类型参数 T 是序列的值类型，因此它必定是数值类型。对于模板主体中的函数 static_assert()，当 T 不是整型也不是浮点型时，它的第一个参数会为 false，这时会生成一条包含第二个字符串参数的编译时错误消息。这里使用的断言模板定义在头文件 type_traits 中，模板中还有一些其他的编译时模板类型参数检查断言。这个构造函数的三个参数都有默认值，因此它也可以作为无参构造函数。这三个参数分别用来初始化值、指定一个值到另一个值的增量，以及指定值的个数。因此默认定义了又有两个值的元素段：0 和 1。编译器会在需要时，提供适当的拷贝构造函数。

另有两个成员函数生成，然后返回元素段的开始和结束迭代器。结束迭代器的成员变量 value 的值为最后一个 value+1。结束迭代器是通过修改开始迭代器的 value 生成的。Numeric_Itemtor<T>模板类型的声明在其定义之前是必要的，因为还没有定义迭代器类型模板。Numeric_Iterator<T> 模板被指定为这个模板的友元类，这样 Numeric_Iterator<T> 的实例就可以访问 Numeric_Range<T> 的私有成员。Numeric_Range<T> 模板也需要成为 Numeric_Iterator<T> 的友元类，因为 Numeric_Range<T> 的成员函数 end() 需要访问 Numeric_Iterator<T> 的一个私有成员。

这个迭代器的模板类型定义如下：

```
// Iterator class template- it's a forward iterator
template<typename T>
class Numeric_Iterator : public std::iterator < std::forward_iterator_tag, T >
{
    friend class Numeric_Range < T >;
protected:
    Numeric_Range<T>& range;                       // Reference to the range for this iterator
    T value;                                       // Value pointed to
public:
    explicit Numeric_Iterator(Numeric_Range<T>& a_range) : range {a_range}, value {a_range.start} {};

    // Assignment operator
    Numeric_Iterator& operator=(const Numeric_Iterator& src)
    {
        range = src.range;
        value = src.value;
    }

    // Dereference an iterator
    T& operator*()
    {
        // When the value is one step more than the last, it's an end iterator
        if (value == static_cast<T>(range.start + range.count*range.step))
        {
            throw std::logic_error("Cannot dereference an end iterator.");
        }
        return value;
    }

    // Prefix increment operator
    Numeric_Iterator& operator++()
    {
        // When the value is one step more than the last, it's an end iterator
        if (value == static_cast<T>(range.start + range.count*range.step))
        {
            throw std::logic_error("Cannot increment an end iterator.");
        }
        value += range.step;                         // Increment the value by the range step
        return *this;
    }

    // Postfix increment operator
    Numeric_Iterator operator++(int)
    {
        // When the value is one step more than the last, it's an end iterator
        if (value == static_cast<T>(range.start + range.count*range.step))
        {
            throw std::logic_error("Cannot increment an end iterator.");
        }
        auto temp = *this;
        value += range.step;                         // Increment the value by the range step
        return temp;                                 // The iterator before it's incremented
    }

    // Comparisons
    bool operator<(const Numeric_Iterator& iter) const { return value < iter.value; }
    bool operator==(const Numeric_Iterator& iter) const { return value == iter.value; }
    bool operator!=(const Numeric_Iterator& iter) const { return value != iter.value; }
    bool operator>(const Numeric_Iterator& iter) const { return value > iter.value; }
    bool operator<=(const Numeric_Iterator& iter) const { *this < iter || *this == iter; }
    bool operator>=(const Numeric_Iterator& iter) const { *this > iter || *this == iter; }
};
#endif
```

代码看起来虽多，却很简单直白。这个迭代器有一个成员变量，它保存了一个和它相关联的 Numeric_Range 对象的引用，另外还保存了它所指向元素的值。迭代器的构造函数的参数是一个 Numeric_Range 对象的引用。构造函数用参数初始化成员变量 range，并将成员变量 value 的值设为 Numeric_Range 的 start。

还定义了一些解引用运算符、前缀或后缀自增运算符以及一套比较运算符。对元素段的结束迭代器的解引用或自增都是非法的，因此如果操作数是结束迭代器，那么自增运算符函数和解引用运算符函数都会拋出异常；这表明成员变量 value 超出了元素段中的最后一个值。为了简单，选择拋出一个标准异常。

头文件 Numeric_Range.h 的完整内容如下：

```
// Exercising the Numeric_Range template
#include <algorithm> // For copy()
#include <numeric> // For accumulate()
#include <iostream> // For standard streams
#include <vector> // For vector container
#include "Numeric_Range.h" // For Numeric_Range<T> & Numeric_Iterator<T>

int main()
{
    Numeric_Range<double> range {1.5, 0.5, 5};
    auto first = range.begin();
    auto last = range.end();
    std::copy(first, last, std::ostream_iterator<double>(std::cout, "  "));
    std::cout << "\nSum = " << std::accumulate(std::begin(range), std::end(range), 0.0) << std::endl;

    // Initializing a container from a Numeric_Range
    Numeric_Range<long> numbers {15L, 4L, 10};
    std::vector<long> data {std::begin(numbers), std::end(numbers)};
    std::cout << "\nValues in vector are:\n";
    std::copy(std::begin(data), std::end(data), std::ostream_iterator<long>(std::cout, "  "));
    std::cout << std::endl;

    // List the values in a range
    std::cout << "\nThe values in the numbers range are:\n";
    for (auto n : numbers)
        std::cout << n << " ";
    std::cout << std::endl;
}
```

运行结果为：
1.5 2 2.5 3 3.5
Sum = 12.5
Values in vector are:
15 19 23 27 31 35 39 43 47 51
The values in the numbers range are:
15 19 23 27 31 35 39 43 47 51

生成的第一个 Numeric_Range 实例有 5 个 double 型元素，它们从 1.5 开始，每次增加 0.5。Numeric_Range 的迭代器用来在 copy() 算法中将值复制到 ostream_iterator。这表明算法可以接受这个迭代器。第二个 Numeric_Range 实例有 10 个 long 型元素。在 vector 容器的初始化列表中，使用开始和结束迭代器，然后用 copy() 算法输出 vector 中的元素。最后，为了演示它的工作原理，以 for 循环的方式输出它的值。输出表明 Numeric_Range 模板成功创建了整型和浮点型的元素段，我们成功定义了一个可以使用 STL 的迭代器类型。