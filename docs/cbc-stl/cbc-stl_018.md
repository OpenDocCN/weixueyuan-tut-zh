# C++ array 获取（访问）元素

可以通过在方括号中使用索引表达式汸问和使用数组容器的元素，这和标准数组的访问方式相同，例如：

```
values[4] = values[3] + 2.O*values[1];
```

第 5 个元素的值被赋值为右边表达式的值。像这样使用索引时，因为没有做任何边界检査，所以，如果使用越界的索引值去访问或存储元素，就不会被检测到。为了能够检查越界索引值，应该使用成员函数 at():

```
values.at (4) = values.at(3) + 2.O*values.at(1);
```

这和前一条语句的功能相同，除了当传给 at() 的索引是一个越界值时，这时会抛出 std::out_of_rang 异常。应该总是使用 at()，除非确定索引没有越界。这也产生了一个疑问，为什么 operator[]() 的实现没有进行边界检查？答案是因为性能。如果每次访问元素，都去检查索引值，无疑会产生很多开销。当不存在越界访问的可能时，就能避免这种开销。

数组对象的 size() 函数能够返回 size_t 类型的元素个数值，所以能够像下面这样去计算数组所有元素的和：

```
double total {};
for(size_t i {} ; i < values.size() ; ++i)
{
    total += values[i];
}
```

size() 函数的存在，为数组容器提供了标准数组所没有的优势，数组元素能够知道它包含多少元素。接受数组容器作为参数的函数，只需要通过调用容器的成员函数 size()，就能得到元素的个数。不需要去调用 size() 函数来判断一个数组容器是否为空。如果容器中没有元素的话，成员函数 empty() 会返回 true:

```
if(values.empty())
    std::cout << "The container has no elements.\n";
else
    std::cout << "The container has "<< values.size()<<"elements.\n";
```

然而，我们很难想象数组容器没有元素的情形，因为当生成一个数组容器时，它的元素个数就固定了，而且无法改变。生成空数组容器的唯一方法是，将模板的第二个参数指定为 0，这种情况基本不可能发生。然而，对于其他元素可变或者元素可删除的容器来说，它们使用 empty() 时的机制是一样的，因此为它们提供了一个一致性的操作。

对于任何可以使用迭代器的容器，都可以使用基于范围的循环，因此能够更加简便地计算容器中所有元素的和：

```
double total {};
for(auto&& value : values)
    total += value;
```

当然，通过把容器作为参数传给函数，也能达到同样的效果。数组容器的成员函数 front() 和 back() 分别返回第一个元素和最后一个元素的引用。成员函数 data() 同样能够返回 &from()，它是容器底层用来存储元素的标准数组的地址，一般不会用到。

模板函数 get<n>() 是一个辅助函数，它能够获取到容器的第 n 个元素。模板参数的实参必须是一个在编译时可以确定的常量表达式，所以它不能是一个循环变量。它只能访问模板参数指定的元素，编译时会对它进行检查。get<n>() 模板提供了一种不需要在运行时检查，但能用安全的索引值访问元素的方式。下面展示如何使用它：

```
std::array<std::string, 5> words {"one","two","three”,"four","five" };
std::cout << std::get<3>(words) << std::endl; // Output words[3]
std::cout << std::get<6>(words) << std::endl; // Compiler error message!
```

下面是一个示例，展示了关于数组容器你到目前为止所学到的知识：

```
/**示例 1**/
#include <iostream> // For standard streams
#include <iomanip>  // For stream manipulators
#include <array>    // For array<T,N>
int main()
{
    const unsigned int min_wt {100U};
    const unsigned int max_wt {250U};
    const unsigned int wt_step {10U};
    const size_t wt_count {1 + (max_wt - min_wt) / wt_step};

    const unsigned int min_ht {48U};
    const unsigned int max_ht {84U};
    const unsigned int ht_step {2U};
    const size_t ht_count { 1 + (max_ht - min_ht) / ht_step };

    const double lbs_per_kg {2.20462};
    const double ins_per_m {39.3701};

    std::array<unsigned int, wt_count> weight_lbs;
    std::array<unsigned int, ht_count> height_ins;

    // Create weights from lOOlbs in steps of lOlbs
    for (size_t i{}, w{min_wt} ; i < wt_count ; w += wt_step, ++i)
    {
        weight_lbs.at(i) = w;
    }

    //Create heights from 48 inches in steps of 2 inches
    unsigned int h{min_ht};
    for(auto& height : height_ins)
    {
        height = h;
        h += ht_step;
    }
    //Output table headings
    std::cout << std:: setw (7) <<" |";
    for (const auto& w : weight_lbs)
        std::cout << std:: setw (5) << w<<"11";
    std::cout << std::endl;
    // Output line below headings
    for (size_t i{1} ; i < wt_count ; ++i)
        std::cout<<"---------";
    std::cout << std::endl;
    double bmi {};
    unsigned int feet {};
    unsigned int inches {};
    const unsigned int inches_per_foot {12U};
    for (const auto& h : height_ins)
    {
        feet = h / inches_per_foot;
        inches = h % inches_per_foot;
        std::cout << std::setw (2) <<feet <<"'"<<std::setw (2) << inches <<"\""<<"|";
        std::cout << std::fixed <<std::setprecision(1);
        for (const auto& w : weight_lbs)
        {
            bmi = h / ins_per_m;
            bmi = (w / lbs_per_kg) / (bmi*bmi);
            std::cout << std:: setw (2) <<""<<bmi <<" |";
        }
        std::cout << std::endl;
    }
    for (size_t i {1} ; i < wt_count ; ++i)
        std::cout << "---------";
    std::cout << "\nBMI from 18.5 to 24.9 is normal" << std::endl;
}
```

本节中，不再展示这个示例的输出结果，因为可能会占据很多篇幅。这里有两套参数，每套定义了 4 个有关身高、体重范围的常量，它们被包含在 BMI 表中。因为身高、体重都是整数、非负数，所以存放在数组容器中的身高和体重都是 unsigned int 类型的元素。

在循环中，可以用适当的值初始化容器。第一个循环展示了 at() 函数的使用，这里也可以放心地使用 weight_lbs[i]。接下来的两个循环分别输出了表的列头，以及一条用来分隔表头和表的横线。数据表是以循环嵌套的方式生成的。外循环遍历身高并输出英尺和英寸的最左一列的身高。内循环遍历体重，输出当前身高每行的 BMI 值。