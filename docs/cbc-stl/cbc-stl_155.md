# C++ valarray 用法（对象初始化和成员函数）详解

定义在 valarray 头文件中的 valarray 类模板定义了保存和操作数值序列的对象的类型，主要用来处理整数和浮点数，但也能够用来保存类类型的对象，只要类满足一些条件：

*   类不能是抽象的。
*   public 构造函数必须包含默认的构造函数和拷贝构造函数。
*   析构函数必须是 public。
*   类必须定义赋值运算符，而且必须是 public。
*   类不能重载 operator&()。
*   成员函数不能拋出异常。
*   不能保存引用或 valarray 中用 const、volatile 修饰的对象。

如果类满足所有这些约束，就可以使用它了。

valarray 模板为数值数据处理提供的功能比任何序列容器（例如 vector）都多。首先，最重要的是，它被设计为允许编译器以一种不应用到序列容器的方式来优化它的操作性能。但是，编译器是否优化并不依赖 valarray 操作的实现。

其次，有相当数量的一元和二元运算都是应用到 valarray 对象的内置类型上的。然后，有相当数量的内置一元函数可以将定义在 cmath 头文件中的运算应用到每个元素上。最后，valarray 类型内置提供了将数据作为多维数组使用的能力。

生成一个 valarray 对象很容易。下面是一些示例：

```
std::valarray<int> numbers (15); // 15 elements with default initial values 0
std::valarray<size_t> sizes {1, 2, 3}; // 3 elements with values 1 2 and 3
std::valarray<size_t> copy_sizes {sizes}; // 3 elements with values 1 2 and 3
std::valarray<double> values;  // Empty array
std::valarray<double> data(3.14, 10); // 10 elements with values 3.14
```

每个构造函数都生成了有给定元素数目的对象。在最后一条语句中，使用圆括号来定义 data 是必要的；如果使用花括号，data 会包含 3.14 和 10 两个元素。也可以用从普通数组得到的一定个数的值来初始化 valarray 对象。例如：

```
int vals[] {2, 4, 6, 8, 10, 12, 14};
std::valarray<int> valsl {vals, 5};   // 5 elements from vals: 2 4 6 8 10
std::valarray<int> vals2 {vals + 1, 4}; // 4 elements from vals: 4 6 8 10
```

后面会介绍其他的构造函数，因为它们有一些必须解释的参数类型。

## valarray 对象的基本操作

valarray 对象和 array 容器一样，不能添加或删除元素。但是，能够改变 valarray 容器中的元素个数，为它们赋新值。例如：

```
data.resize(50, 1.5); // 50 elements with value 1.5
```

在这个操作之前，如果 data 中保存有元素，就会丢失它们的值。当需要得到元素的个数时，可以调用成员函数 size()。

成员函数 swap() 可以交换当前对象和作为参数传入的另一个 valarray 对象的元素。例如：

```
std::valarray<size_t> sizes_3 {1, 2, 3};
std::valarray<size_t> sizes_4 {2, 3, 4, 5};
sizes_3.swap(sizes_4); // sizes_3 now has 4 elements and sizes_4 has 3
```

valarray 对象中包含的元素个数可以不同，但显然两个对象中的元素必须是相同类型的。成员函数 swap() 没有返回值。非成员函数 swap() 函数模板做的事是一样的，因此最后一条语句可以替换为：

```
std::swap(sizes_3,sizes_4); // Calls sizes_3.swap(sizes_4)
```

可以调用 valarray 的成员函数 min() 和 max() 来查找元素的最小值和最大值。例如：

```
std::cout << "The elements are from " << sizes_4.min () << " to "<< sizes_4.max() << '\n';
```

为了能够正常工作，元素必须是支持 operator<() 的类型。

成员函数 sum() 会返回元素的和，它是使用 += 运算符计算出来的。因此，可以在 valarray 中按如下方式计算元素的平均值：

```
std::cout << "The average of the elements " << sizes_4.sum()/sizes_4.size() << '\n';
```

这比使用 accumulate() 算法要简单得多。

valarray 没有返回元素迭代器的成员函数，但有专门的非成员函数版本的 begin() 和 end() 可以返回随机访问迭代器。这使我们能够使用基于范围的 for 循环来访问 valarray 中的元素，并且可以将算法应用到元素上；后面你会看到一些示例。不能对 valarray 使用插入迭代器，因为没有做这些事的成员函数，这也是它的大小不变的原因。

有两个成员函数可以对元素进行移位(是对序列进行移位而不是移位单个元素值中的比特)。首先，成员函数 shift() 会将全部的元素序列移动由参数指定的位数。函数会返回一个新的 valarray 对象作为结果，保持原序列不变。如果参数是正数，元素会被左移；如果是负数，会右移元素。这看起来很像位移。

元素被移位之后，序列的左边或右边会为 0 或其他等同的类型。当然，如果不将移位操作之后的结果存回原容器，原对象是不会改变的。下面是一些展示它如何工作的代码：

```
std::valarray<int> d1 {1, 2, 3, 4, 5, 6, 7, 8, 9};
auto d2 = d1.shift(2);  // Shift left 2 positions
for(int n : d2) std::cout << n <<' ';
std::cout << '\n';//Result: 345678900
auto d3 = d1.shift(-3);//Shift right 3 positions
std::copy(std::begin(d3), std::end(d3),std::ostream_iterator<int>{std::cout, " "});
std::cout << std::endl;
// Result: 0 0 0 1 2 3 4 5 6
```

注释解释发生了什么。为了展示可以使用的输出方式，在两个示例中用了不同的方式来输出结果。valarray 模板为对象定义了赋值运算符，所以如果想替换原始序列，可以这样写：

```
d1 = d1.shift(2); // Shift d1 left 2 positions
```

元素移位的第二种方式是使用成员函数 cshift()，它会将元素序列循环移动由参数指定的数目的位置。序列会从左边或右边循环移动，这取决于参数是正数还是负数。这个成员函数也会返回一个新的对象。下面是一个示例：

```
std::valarray<int> d1 {1, 2, 3, 4, 5, 6, 7, 8, 9};
auto d2 = d1.shift(2); // Result d2 contains: 3 4 5 6 7 8 9 1 2
auto d3 = d1.cshift(-3);// Result d3 contains: 7 8 9 1 2 3 4 5 6
```

apply() 函数是 valarray 的一个非常强大的成员函数，它可以将一个函数应用到每个元素上，并返回一个新的 valarray 对象为结果。valarray 类模板中定义了两个 apply() 函数模板：

```
valarray<T> apply(T func(T)) const;
valarray<T> apply(T func(const T&)) const;
```

有三件事需要注意。第一，所有版本都是 const，所以函数不能修改原始元素。第二，参数是一个有特定形式的函数，这个函数以 T 类型或 T 的 const 引用为参数，并返回 T 类型的值；如果 apply() 使用的参数不符合这些条件，将无法通过编译。第三，返回值是 valarray<T> 类型，因此返回值总是一个和原序列有相同类型和大小的数组。

下面是一个使用成员函数 apply() 的示例：

```
std::valarray<double> time {0.0，1.0, 2.0, 3.0, 4.0, 5.0, 6.0, 7.0, 8.0, 9.0}; // Seconds
auto distances = time.apply([](double t)
{
    const static double g {32.0}; // Acceleration due to gravity ft/sec/sec
    return 0.5*g*t*t;
}); // Result: 0 16 64 144 256 400 576 784 1024 1296
```

如果从高层建筑向下丢砖块，distances 对象可以计算出，过了相应的秒数后，砖块下落了多少距离；为了使最后结果有效，建筑的高度必须超过 1296 英尺。注意，不能使用从闭合区域捕获的值作为参数的 lambda 表达式，因为这和函数模板中参数的规格不匹配。例如，下面的代码就无法通过编译：

```
const double g {32.0};
auto distances = times.apply ([g] (double t) { return 0.5*g*t*t; }); // Won*t compile!
```

在 lambda 表达式中以值捕获 g 会改变它的类型，以至于它不符合应用模板的规范。对于可以作为 apply() 参数的 lambda 表达式，捕获语句必须为空。必须有一个和数组类型相同的参数，并返回一个这种类型的值。

valarray 头文件定义了许多来自于 cmath 头文件的函数的重载版本，因此它们能够应用到 valarray 对象的所有元素上。接受一个 valarray 对象为参数的函数有：

abs(), pow(), sqrt(), exp(), log(), log10(), sin(), cos(), tan(), asin(), acos(), atan(), atan2(), sinh(),cosh(), tanh()

下面是一个将这一节的代码段组合到一起的示例，它提供了一次将 cmath 函数用到 valarray 对象上的机会：

```
// Dropping bricks safely from a tall building using valarray objects
#include <numeric>                                       // For iota()
#include <iostream>                                      // For standard streams
#include <iomanip>                                       // For stream manipulators
#include <algorithm>                                     // For for_each()
#include <valarray>                                      // For valarray
const static double g {32.0};                            // Acceleration due to gravity ft/sec/sec

int main()
{
    double height {};                                      // Building height
    std::cout << "Enter the approximate height of the building in feet: ";
    std::cin >> height;

    // Calculate brick flight time in seconds
    double end_time {std::sqrt(2 * height / g)};
    size_t max_time {1 + static_cast<size_t>(end_time + 0.5)};

    std::valarray<double> times(max_time + 1);               // Array to accommodate times
    std::iota(std::begin(times), std::end(times), 0);        // Initialize: 0 to max_time
    *(std::end(times) - 1) = end_time;                       // Set the last time value

    // Calculate distances each second
    auto distances = times.apply([](double t) { return 0.5*g*t*t; });

    // Calculate speed each second
    auto v_fps = sqrt(distances.apply([](double d) { return 2 * g*d; }));

    // Lambda expression to output results
    auto print = [](double v) { std::cout << std::setw(5) << static_cast<int>(std::round(v)); };

    // Output the times - the last is a special case...
    std::cout << "Time (seconds): ";
    std::for_each(std::begin(times), std::end(times) - 1, print);
    std::cout << std::setw(5) << std::fixed << std::setprecision(2) << *(std::end(times) - 1);

    std::cout << "\nDistances(feet):";
    std::for_each(std::begin(distances), std::end(distances), print);

    std::cout << "\nVelocity(fps):  ";
    std::for_each(std::begin(v_fps), std::end(v_fps), print);

    // Get velocities in mph and output them
    auto v_mph = v_fps.apply([](double v) { return v * 60 / 88; });
    std::cout << "\nVelocity(mph):  ";
    std::for_each(std::begin(v_mph), std::end(v_mph), print);
    std::cout << std::endl;
}
```

这样就能够确定从高层建筑丟下砖块时发生了什么。下面是一些从迪拜塔得到的示例输出，为了避免砖块撞到墙壁，假设是从一根足够长的杆子上扔下砖块的：

Enter the approximate height of the building in feet: 2722
Time (seconds) : 0 1 2 3 4 5 6 7 8 9 10 11 12   13 13.04
Distances(feet): 0 16 64 144 256 400 576 784 1024 1296 1600 1936 2304   2704 2722
Velocity (fps) : 0 32 64 96 128 160 192 224 256 288 320 352 384 416 417
Velocity (mph) : 0 22 44 65 87 109 131 153 175 196 218 240 262  284 285

首先，如果我们真的这么做了，肯定会坐牢，甚至更糟。其实，计算时忽略了阻力，但这本书是关于 STL 的，不考虑物理因素。最后，可以通过花费的时间乘以加速度得到速度，但那样就不能将 sqrt() 应用到 valarray 上了，不是吗？

所有的代码都很简单。常量 g 被定义在全局作用域内，因为这样方便在代码的不同位置使用，包括 lambda 表达式。times 数组中保存的时间是以秒为单位的，用 iota() 算法从 0 开始填充元素。最后的时间值，对应于砖块撞到地面时，几乎可以肯定不是整数，所以保存的值是具体的。用 for_each() 来产生输出，因为它比 copy() 更能控制输出值和输出流迭代器。最后的时间值不可能是整数秒，因此它被当作输出中的特殊情况。lambda 表达式 print 是显式定义的，因此它能被重用，从而输出每组值。

为了得到或设置 valarray 中给定索引的元素，可以使用下标运算符 `[]`，但下标运算符可以做的事不止于此，在本章的后面你就会看到。