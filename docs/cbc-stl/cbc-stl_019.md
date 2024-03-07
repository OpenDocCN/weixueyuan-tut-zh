# C++ array 迭代器及用法

数组模板定义了成员函数 begin() 和 end()，分别返回指向第一个元素和最后一个元素的下一个位置的随机访问迭代器。如前面章节所述，随机访问迭代器具有最多的功能，能使用它进行全部的操作。可以在循环中显式地使用迭代器来设置 height_ins 容器的值：

```
unsigned int h {min_ht};
auto first = height_ins.begin();
auto last = height_ins.end () ;
while (first != last)
{
    *first++=h;
    h += ht_step;
}
```

迭代器对象是由 array 对象的成员函数 begin() 和 end() 返回的。使用 auto 时小需要担心迭代的实际类型，除非你要自己考虑，在本例中它们是 std::array<unsigned int,19>::iterator 类型，这意味着 iterator 类型被定义在 array<T,N> 类型中。可以看出，可以像使用普通指针那样上使用迭代器对象。在保存了元素值后，使用后缀 ++ 运算符对 first 进行自增。当 first 等于 end 时，所有的元素都被设完值，循环结束。

如前面章节所述，最好用全局的 begin() 和 end() 函数来从容器中获取迭代器，因为它们是通用的，first 和 last 可以像下面这样定义：

```
auto first = std::begin(height_ins);
auto last = std::end (height_ins);
```

记住，当迭代器指向容器中的一个特定元素时，它们没有保留任何关于容器本身的信息，所以我们无法从迭代器中判断，它是指向 array 容器还是指向 vector 容器。容器中的一段元素可以由迭代器指定，这让我们有了对它们使用算法的可能。

定义在 algorithm 头文件中的 generate() 函数模板，提供了用函数对象计算值、初始化一切元素的可能。我们可以像这样重写之前用来初始化 height_ins 容器的代码段：

```
unsigned int height {};
std::generate(std::begin(height_ins), std::end(height_ins),[height, &min_ht, &ht_step]()mutable
{ return height += height == 0 ? min_ht : ht_step; });
```

为容器元素设置值的语句现在减少到两条，也不再需要显式的循环语句。第一条语句定义了一个变量用来保存元素的初始值。genemte() 的前两个参数分别是开始迭代器和结束迭代器，用来指定需要设置值的元素的范围。第三个参数是一个 lambda 表达式。lambda 表达式以引用的方式捕获 min_hi、ht_step。mutable 关键使 lambda 表达式能够更新 height 局部副本的值，它是以值引用的方式捕获的。

在 return 语句中，lambda 第一次执行后，height 的局部副本的值被设为 min_ht。然后，随着 lambda 的每次调用，height 都会增加 ht_step。 在 lambda 表达式中，以值引用的方式捕获的变量局部副本的值会被一直保存，这一机制正好满足了我们的要求。

假定要用连续的递增值初始化一个数组容器，这里有一个函数模板 iota() 可以做到，它定义在头文件 numeric 中。这里有一个它的用法示例：

```
std::array<double, 10> values;
std::iota(std::begin(values), std::end(values),10.0);
    elements to 10.0 to 19.0
```

前两个参数是迭代器，用来定义需要设置值的元素的范围。第三个参数是第一个元素要设置的值，通过递增运算生成了随后每一个元素的值。iota()函数的使用并不仅限于数值。元素可以设为任意类型的值，只要这些类型支持 operator++()。

注意，不要忘记算法是独立于容器类型的，对于任何具有指定类型迭代器的容器来说，算法都可以应用到它们的元素上。generate() 和 iota() 函数模板只需要正向迭代器，所以用来指定任何容器的元素范围的迭代器都能发挥作用。

容器定义了成员函数 cbegin() 和 cend()，它们可以返回 const 迭代器。当只想访问元素时，应该使用 const 迭代器。对于 non-const 迭代器，最好使用全局的 cbegin 和 cend() 来获取。全局函数或成员函数 rbegin() 和 rend() 可以分别得到指向最一个元素和第一个元素前一个位置的反向迭代器。函数 crbegin() 和 crend() 可以返回 const 反向迭代器。我们可以用反向迭代器以逆序方式处理元素。例如：

```
std::array<double, 5> these {1.0, 2.0, 3.0, 4.0, 5.0};
double sum {};
auto start = std::rbegin(these);
auto finish = std::rend(these);
while(start != finish)
    sum += *(start++};
std::cout << "The sum of elements in reverse order is " << sum << std::endl;
```

在循环中，从最后一个元素幵始，我们计算出了所有元素的和。结束迭代器指向第一个元素之前的位置，所以 sum 加上第一个元素后，循环就结朿了。在反向迭代器上使用递增运算符，会让迭代器用一种和普通正向迭代器移动方向相反的方式移动。我们也可以使用 for 循环：

```
for (auto iter = std::rbegin (these); iter != std::rend(these); ++iter)
    sum += *iter;
```

因为数组容器实例的元素个数固定，所以无法使用插入迭代器。插入迭代器通常用来向容器中添加元素。