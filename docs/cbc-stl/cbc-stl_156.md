# C++ valarray 对象适用运算符详解

这里有 4 个可以应用到 valarray 对象的一元运算符：+、-、~ 和 !。效果是将运算符应用到数组的每个元素上，并返回一个新的 valarray 对象作为结果，不改变原对象。只能将它们应用到元素类型支持这些运算符的 valarray 对象上应用！运算符得到的新元素总为布尔类型，所以这个运算的结果是一个 valarray<bool>类型的对象。本章的后面会讨论它的使用场景。

其他运算符生成的结果必须和这个运算的合法原始元素是相同的类型。例如，一元减法运算符只能反转有符号数的元素的符号，因此它无法用于无符号类型。下面的代码展示了 ! 运算符的效果：

```
std::valarray<int> data {2, 0, -2, 4, -4};
auto result = !data; //result is of type valarray bool
std::copy(std::begin(result) , std::end(result),std::ostream_iterator<bool>{std::cout<<std::boolalpha, " "});
std::cout << std::endl;
// Output: false true false false false
```

当 ! 被应用到 data 中的值时，值首先会被隐式转换为布尔，然后运算符会被应用到结果上。如果想用 copy() 算法以布尔值的形式输出值，结果肯定是 true false true true true，这解释了为什么上述代码的输出是注释显示的那样。

~ 运算符是位的 NOT （位的取反）或 1 的补码。下面是一个示例：

```
std::valarray<int> data {2, 0, -2, 4, -4}; // 0x00000002 0 Oxfffffffe 0x00000004 Oxfffffffc
auto result = ~data;
std::copy(std::begin(result), std::end(result),std::ostream_iterator<int> {std::cout, " "});
std::cout << std::endl;
// Output: -3 -1 1 -5 3
```

为了生成结果中的元素，可以通过对原始整数值取反的方式来得到。例如，data 中的第二个元素的比特位全为 0，因此应用 ~ 产生的值的比特位全为 1，对应于十进制的 -1。

+ 运算符对数值没有效果；- 运算符会改变符号。例如：

```
std::valarray<int> data {2, 0, -2, 4, -4};
auto result = -data;
std::copy(std::begin(result), std::end(result),std::ostream_iterator<int> {std::cout," "});
std::cout << std::endl;
// Output: -2 0 2 -4 4
```

当然，也可以覆盖原始对象。为了使代码不那么杂乱，从现在起，假设为 std::valarray 使用的 using 指令生效，然后去掉代码中用于对 valarray 类型进行限定的 std 命名空间。

## 用于 valarray 对象的复合赋值运算符

所有的复合赋值运算符的左操作数都是 valarray 对象，右操作数是一个和所保存的元素同类型的值。在这种情况下，值会被合并到每个元素的值上，合并方式由运算符决定。右操作数也可以是和左操作数有相同元素个数和元素类型的 valarray 对象。在这种情况下，左操作数会因为合并了右操作数对应的元素而被修改。这一类的运算符有：

1) 复合算术赋值运算符 +=、-=、*=、/=、％=。例如：

```
valarray<int> v1 {1, 2, 3, 4};
valarray<int> v2 {3, 4, 3, 4};
v1+= 3; // v1 is: 4 5 6 7
v1 -= v2;   // v1 is: 1 1 3 3
```

2) 复合位操作赋值运算符 &=、|=、A=。例如：

```
valarray<int> v1{1, 2, 4, 8};
valarray<int> v2 {4, 8, 16, 32};
v1 |= 4;    // v1 is: 5 6 4 12
v1 &= v2;   // v1 is: 4 0 0 0
v1 ^= v2;   // v1 is: 0 8 16 32
```

3) 复合移位赋值运算符 >>=、<<=。例如：

```
valarray<int> v1 {1, 2, 3, 4};
valarray<int> v2 {4, 8, 16, 32};
v2 <<= v1; // v2 is: 8 32 128 512
v2 >>= 2; // v1 is: 2 8 32 128
```

复合位操作和复合移位运算符一般用于整数类型。

## valarray 对象的二元运算

可以将能够应用到基本类型值的任何二元运算符应用到 valarray 对象上，也能够合并两个 valarray 对象的相应元素，或者将 valarray 中的元素和一个同类型的值合并。在 valarray 头文件中定义了下面这些二元运算符的非成员操作符函数：

*   算术运算符 +、-、*、/、%；
*   位操作运算符 &、|、^；
*   位移运算符 >>、<<；
*   逻辑运算符 &&、||；

这些运算符有不同的版本，可以应用到一个 valarmy<T> 对象和一个 T 类型对象上、一个 T 类型对象和一个 valarray 对象上，或者应用到两个 valarray 对象上。两个 valarray 对象上的运算需要它们有相同个数的相同类型的元素。逻辑运算符会返回一个和 valarray 操作数有相同个数元素的 valarray<bool> 对象。其他的运算符会返回一个和 valarray 操作数有相同类型且相同个数元素的 valarray 对象。

将 valarray 对象的内容输出到 cout 来说明发生了什么是很有用处的：

```
// perline is the number output per line, width is the field width
template<typename T>
void print(const std::valarray<T> values, size_t perline = 8, size_t width = 8)
{
    size_t n {};
    for (const auto& value : values)
    {
        std::cout << std::setw(width) << value << " "; if(++n % perline == 0) std::cout << std::endl;
    }
    if (n % perline != 0) std::cout << std::endl;
    std::cout << std::endl;
}
```

这个函数适用于包含任何 T 类型元素的 valarray 对象，只要 T 类型元素支持输出流的 operator<<()。

这里不会为所有的二元运算符都举一个示例，只做少量说明。下面是一个将二元算术运算符用于 valarray 对象的示例：

```
valarray<int> even {2, 4, 6, 8};
valarray<int> odd {3, 5, 7, 9};
auto r1 = even + 2;
print(r1, 4, 3);    // r1 contains: 4 6 8 10
auto r2 = 2*r1 + odd;
print(r2, 4, 3);    // r2 contains: 11 17 23 29
r1 += 2*odd - 4* (r2 - even);
print(r1, 4, 3);    // r1 contains: -26 -36 -4 6 -56
```

最后一条语句使用复合赋值运算符的成员函数来加上右操作数（一个表达式）的结果。 这说明可以像数值那样来合并包含 valarray 对象的运算，包括括号的使用。下面是一个使用位移运算的示例：

```
print (odd <<3, 4, 4) ; // Output is: 24 40 56 72
```

print() 的第一个参数是将 odd 中的元素向左移动 3 个比特位之后得到的 valarray 对象。 每行输出 4 个值，每个值的宽度是 4。

valarray 头文件中也定义了一些非成员函数，可以用来比较两个 valarray<T> 对象，或者将 valarray<T> 对象的每一个元素和 T 类型的值做比较。比较的结果保存在 valarray<bool> 对象中，它和 valarray 包含相同个数的元素。支持的运算是 ==、！=、<、<=、> 和 >=。下面是一些使用这些运算符的示例：

```
valarray<int> even {2, 4, 6, 8};
valarray<int> odd {3, 5, 7, 9};
std::cout << std::boolalpha;
print(even + 1 == odd, 4, 6);
//Output is: true true true true
auto result = (odd < 5) && (even +3 != odd);
print(result); //Output is: true false false false
```

倒数第二条语句用二元运算符 && 来合并比较结果。结果说明，当 odd 元素小于 5 时，even 中相应的元素加上 3 后不等于 odd 中的元素；只有在比较 even 和 odd 的第一个元素时表达式才为 true，因为 odd 中只有第一个元素能使 odd<5，能使 even + 3 != odd 总为 true。

有一些定义了适用于 valarray 的元素子集的辅助类。主要的辅助类是 std::slice 和 std::gslice。为这些代码去掉 std 命名空间限定符。在更深入了解能用 valarray 做什么之前，让我们先探索如何将辅助类用于 valarray。