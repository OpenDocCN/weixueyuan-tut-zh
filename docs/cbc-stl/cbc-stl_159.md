# C++ duration（STL duration）模板用法详解

duration (持续时间) 是定义为时间刻度数的时间间隔，可以指定一个时间刻度是多少秒。因此，时间刻度是衡量时间长短的基础。duration 模板的实例类型的对象定义了 duration。时间刻度所表示的默认时间间隔是 1 秒，但可以将它定义为更多秒或秒几分之一。例如，如果定义时间刻度为 3600 秒，但意味着 10 个 duration 就是 10 个小时；也能够定义时间刻度为一秒的十分之一，在这种情况下，10 个 duration 表示 1 秒。

## 定义 duration

chrono 头文件中的 std::chrono::duration<T,P> 模板类型表不 duration。模板参数 T 是值的类型，一般是基本的算术类型，并且参数 P 对应的值是时间刻度所表示的秒数，它所对应的值为 1 秒。必须用 ratio 类型指定 P 的值，默认为 ratio<1>。下面是 duration 的一些示例：

```
std::chrono::duration<int,std::milli> IBM650_divide {15}; // A tick is 1 millisecond so 15 milliseconds std::chrono::duration<int> minute {60}; // A tick is 1 second by default so 60 seconds
std::chrono::duration<double, ratio<60>>hour {60}; // A tick is 60 seconds so 60 minutes
//A tick is a microsecond so 1 millisecond std::chrono::duration<long, std::micro> millisec {1000L};
//A tick is fifth of a second so 1.1 seconds
std::chrono::duration<double, ratio<1,5>> tiny {5.5};
```

第 1 条语句使用从 ratio 头文件中获取的对应于 ratio<1，1000> 的别名 milli。第 2 条语句省略了第二个模板参数的值，因此是 ratio<1>，这意味着持续时间以 1 秒为单位。

在第 3 条语句中，ratio<60> 模板参数值指定了时间刻度为 60 秒，因此 hour 对象的值是按分钟测量的，它的初值表示 1 小时。

第 4 条语句使用了 ratio 头定义的 micro 类型来定义 ratio<1, 1000000>，因此 tick 是毫秒，millisec 变量有一个表示毫秒的初值。最后一条语句定义了一个时间刻度为 1/5 秒的对象，初值是 5.5 的五分之一，它是 1.1 秒。

chrono 头文件为有整数类型值的常用持续时间类型在 std::chrono 命名空间中定义了别名。标准别名是：

nanoseconds<integer_type, std::nano> microseconds<integer_type, std::micr>milliseconds<integer_type, std::milli> seconds<integer_type> minutes<integer_type, std::ratio<60>> hours<integer_type, std::ratio <3600>>

这些别名中，每个整数类型的持续时间值都取决于实现，但 C++14 标准要求 duration 至少为 292 年，可以是正数或负数。在笔者系统上小时和分钟类型将 duration 保存为 int 类型，将其他的保存为 long long 类型。因此前面的代码段可以这样定义 millisec 变量：

```
std::chrono::microseconds millisec {1000}; // Duration is type long long on my system
```

当然，这个定义和原始定义不同。变量的初值表示的是相同的时间间隔（1 毫秒）但这里持续时间的单位是 1 毫秒，而在先前的代码中是 1 微秒。前面的 millisec 的定义许更精确地表示持续时间。

所有可以应用到 duration 对象的算术运算符，都可以被用到左操作数是 duration 对象的复合赋值中，+= 和 -+ 这些运算的右操作数必须是 duration 对象。*= 和 /= 的右操作数必须和作为左操作数的时间刻度数有相同类型的数值，或者可以隐式转换为数值。％= 的右操作数可以是 duration 对象或数值。它们中的每一个都能产生我们期望的结果。例如下面使用 += 的代码：

```
std::chrono:imilliseconds millisec {1}; // Duration is also type long long on my system
```

第一个 += 运算的操作数为相同类型，作为右操作的对象所保存的值会被加到左操作数上。第二个 += 运算的操作数为不同类型，但右操作数会被隐式转换为左操作的类型。这是可能的，因为转换是向有较短时间刻度的 duration 类型转换的。如果向相反的方向转换就不行，所以不能在 += 运算中用 long_time 作为左操作数、用 short_time 作为右操作数。

## duration 之间的算术运算

可以将前缀或后缀自增和自减运算符应用到 duration 对象上，并且可以通过调用成员函数 count() 来得到时间刻度数。下面是示例代码：

```
std::chrono::duration<double, ratio<1, 5>> tiny {5.5}; // Measured in 1/5 second
std::chrono::microseconds very_tiny {100}; // Measured in microseconds
++tiny;
very_tiny--;
std::cout << "tiny = " << tiny.count()<< " very_tiny = " << very_tiny.count()<< std::endl;    // tiny = 6.5 very_tiny = 99
```

可以将任何二元算术运算符 +、-、*、/、％ 应用到 duration 对象上，会得到一个 duration 对象作为结果。这些都是作为非成员运算符函数实现的。下面是一个示例：

```
std::chrono::duration<double, ratio<1, 5>> tiny {5.5}; std::chrono::duration<double, ratio<l, 5>> small {7.5};
auto total = tiny + small;
std::cout << "total = " << total.count() << std::endl;
// total = 13
```

算术运算符也可以用不同类型的 std::chrono::duration<T,P> 模板实例作为操作数，模板的两个参数都可以不同。这是通过使用 common_type<class... T> 模板的特化将两个操作数转换为它们的共有类型来实现的，common_type<class... T> 定义在 type_traits 头文件中。对于 duration<T1，P1> 和 duration<T2, P2> 类型的参数，返回值为 duration 类型 duration<T3，P3>。T3 是 T1 和 T2 的共有类型。这些类型是通过将算术运算应用到这些类型的值来得到的。

P3 是 P1 和 P2 的最大公因数。有一个示例会更清楚一些：

```
std::chrono:imilliseconds ten_minutes {600000}; // A tick is 1 millisecond so 10 minutes
std::chrono::minutes half_hour {30}; // A tick is 1 minute so 30 minutes
auto total = ten_minutes + half_hour; // 40 minutes in common tick period
std::cout <<"total = " << total.count()<< std::endl;
// total = 2400000
```

加法的结果是 40 分钟，因此可以推断出 total 是毫秒类型的对象。下面是另一个示例：

```
std::chrono::minutes ten_minutes {10};  // 10 minutes
std::chrono::duration<double, std::ratio<1, 5>> interval {4500.0}; // 15 minutes
auto total_minutes = ten_minutes + interval;
std::cout << "total minutes = " << total_minutes.count()<< std::endl;
// total minutes = 7500
```

total_minutes 的值是 double 类型。我们知道结果必定是 25 分钟，也就是 1500 秒；结果为 7500，因此时间刻度的长度为 ratio<1，5>，即 1/5 秒。最好尽可能地避免对混合的 duration 类型进行算术运算，因为很容易不知道时间刻度是什么。

所有可以应用到 duration 对象的算术运算都可以用到左操作数是 duration 的复合赋值中。+= 和 -= 的右操作数必须是 duration 对象。*= 和 /= 的右操作数必须和左操作数的时钟刻度类型相同，或者可以隐式转换为那种类型。％= 的右操作数可以是 duration 对象或数值。它们中的每一个都能产生我们想要的结果。例如，下面是使用 += 的示例：

```
std::chrono::minutes short_time {20};
std::chrono::minutes shorter_time {10};
short_time += shorter_time; // 30 minutes
std::chrono::hours long_time {3}; // 3hrs = 180 minutes
short_time += long_time;
std::cout << "short_time = " << short_time.count() << std::endl;
// short_time = 210
```

第一个 += 运算的操作数都为相同类型，因此右操作对象保存的值会被加到左操作数上。第二个 += 运算的操作数为不同类型，但是右操作数可以隐式转换为左操作数的类型。这是可能的，因为转换的是短时钟周期的 duration 类型。相反，就不能转换，因此不能在用 += 时以 long_time 作为左操作数，以 short_time 作为右操作数。

## duration 类型之间的转换

通常，一个 duration 类型总是可以被隐式转换为另一个 duration 类型，如果它们都是浮点型 duration 值的话。当源类型的时钟周期是目的类型的时钟周期的整数倍时，只能对整数值进行隐式转换。下面是一些示例：

```
std::chrono::duration<int, std::ratio<1, 5>> d1 {50}; // 10 seconds
std::chrono::duration<int, std::ratio<1, 10>> d2 {50}; // 5 seconds
std::chrono::duration<int, std::ratio<l, 3>> d3 {45}; // 15 seconds
std::chrono::duration<int, std::ratio<1, 6>> d4 {60}; // 10 seconds
d2 += d1; // OK - implicit conversion of d1
d1 += d2; // Won't compile 1/10 not a multiple of 1/5
d1 += d3; // Won't compile 1/3 not a multiple of 1/5
d4 += d3; //OK - implicit conversion of d3
```

可以显式地使用 dumtion_cast 模板进行强制转换。下面是一个示例，假设 d1 和 d2 都有上述代码定义的初值：

```
d1 += std::chrono::duration_cast<std::chrono::duration<int, std::ratio<l, 5>>>(d2);
std::cout << d1.count() << std::endl; // 75 - i.e.15 seconds
```

第一条语句会用 duration_cast 来使 d2 加到 d1 的运算能够进行。在这个示例中，结果是准确的，但并不总是如此。例如：

```
std::chrono::duration<int, std::ratio<1, 5>> d1 {50}; // 10 seconds
std::chrono::duration<int, std::ratio<1, 10>> d2 {53}; // 5.3 seconds
d1 += std::chrono::duration_cast<std::chrono::duration<int, std::ratio<1, 5>>>(d2);
std::cout << d1.count() << std::endl; // 76 - i.e. 15.2 seconds
```

不能将 duration 值 d1 和 d2 的和表示成 0.2 秒的整数倍，因此结果值稍微超出了。如果 d2 的值是 54，得到的正确结果应该是 77。

duration 类型支持赋值，因此可以将一个 duration 对象的值赋给另一个 duration 对象。如果与这一节开始时描述的条件相符，就可以使用隐式转换；否则需要对右操作数显式转换。例如，可以这样写：

```
std::chrono::duration<int, std::ratio<1, 5>> d1 {50}; // 10 seconds
std::chrono::duration<int, std::ratio<1, 10>> d2 {53}; // 5.3 seconds
d2 = d1;  // d2 is 100 = 10 seconds
```

## 比较 duration

有一整套完整的运算符可以用来比较两个 duration 对象。它们都是由非成员函数实现的，允许比较不同类型的 duration 对象。这个过程可以确定操作数共同的时钟周期，当表示成相同的时钟周期时，就可以比较 duration 的值。例如：

```
std::chrono::duration<int,std::ratio<1, 5>> d1 {50}; // 10 seconds
std::chrono::duratior<int, std::ratio<1, 10>> d2 {50}; // 5 seconds
std::chrono::duration<int, std::ratio<1, 3>> d3 {45}; // 15 seconds
if ((d1 - d2) == (d3 - d1))
    std::cout << "both durations are "<<std::chrono::duration_cast<std::chrono::seconds〉(d1 - d2).count() << " seconds" << std::endl;
```

这里显示了从算术运算得到的 duration 对象的比较方法。它们是相等的，当然也会产生这样的输出。seconds 类型的强制转换允许秒数表示成整数，而不用管结果为 duration 类型。如果想使用非整数的秒数值，可以强制转换为 dumtion<dcmble> 类型。

## duration 常量

chrono 头文件中定义了可以让我们指定 duration 对象的常量的运算符。这些运算符被定义在命名空间 std::literals::chrono_literals 中，命名空间 literals 和 chrono_literals 都是内联的。有了下面的声明之后，就可以对 duration 常量使用常量运算符：

```
using namespace std::literals::chrono_literals;
```

但是，如果指定了下面的声明，上面的声明会被自动包含：

```
using namespace std::chrono;
```

可以将 duration 常量指定为整数或浮点值，后缀指定了时钟周期。这里有 6 个可以使用的后缀：

*   h 是小时，例如 3h 或 3.5h。
*   min 是分钟，例如 20min 或 3.5min。
*   s 是秒，例如 10s 或 1.5s。
*   ms 是毫秒，例如 500ms 或 1.5ms。
*   us 是微秒，例如 500us 或 0.5uso
*   ns 是纳秒，例如 2ns 或 3.5ns。

如果一个 duration 常量有整数值，它会从这些被定义在 chrono 头文件中的常量得到一个合适的别名。因此 24h 是一个 std::chrono::hours 类型的常量，25ms 的类型是 std::chrono::milliseconds。如果常量的值不是整数，这个常量会是浮点值类型的 duration 类型。浮点值的周期取决于后缀；对于后缀 h、min、s、ms、us 和 n，时钟周期分别为 ratio<3600>、ratio<60>、 ratio<1>、milli、micro 和 nano。

下面举例说明使用它们的一些方式：

```
using namespace std::literals::chrono_literals;
std::chrono::seconds elapsed {10}; // 10 seconds
elapsed += 2min; // Adding type minutes to type seconds: 130 seconds
elapsed -= 15s; // 115 seconds
```

如示例所示，当需要改变间隔的长短时，duration 常量是非常有用的。需要记住的是，为了能够进行算术运算，作为右操作数的时钟周期必须是作为左操作数的时钟周期的整数倍。例如:

```
elapsed += 100ns; // Won't compile!
```

变量 lapsed 的时钟周期为 1，不能向它加一个周期小于 1 的 duration。

可以用常量将同类型的变量定义为常量。例如：

```
auto some_time = 10s;   // Variable of type seconds, value 10
elapsed = 3min - some_time;// Set to difference between literal and variable: result 170
some_time *= 2; // Doubles the value - now 20s
const auto FIVE_SEC = 5s; // Cannot be changed
elapsed = 2s + (elapsed - FIVE_SEC)/5;
// Result 35
```

这里，some_time 是一个 seconds 类型的变量，它的类型为 duration<long long，ratio<1>>，值为 10。第 3 条语句说明可以改变 some_time 的值。FIVE_SEC 是 const seconds 类型的值，因此不能改变它的值。最后一条语句展示了一个包含一个 duration 常量、一个 duration 变量、一个常量类型的 seconds 对象，以及一个整数常量的算术表达式。