# C++ queue(STL queue)用法详解

只能访问 queue<T> 容器适配器的第一个和最后一个元素。只能在容器的末尾添加新元素，只能从头部移除元素。

许多程序都使用了 queue 容器。queue 容器可以用来表示超市的结账队列或服务器上等待执行的数据库事务队列。对于任何需要用 FIFO 准则处理的序列来说，使用 queue 容器适配器都是好的选择。

图 1 展示了一个 queue 容器及其一些基本操作：
![](img/b445642f9e26c984d39ac93a487daf07.jpg)图 1 queue 容器
queue 的生成方式和 stack 相同，下面展示如何创建一个保存字符串对象的 queue:

```
std::queue<std::string> words;
```

也可以使用拷贝构造函数：

```
std::queue<std::string> copy_words {words}; // A duplicate of words
```

stack<T>、queue<T> 这类适配器类都默认封装了一个 deque<T> 容器，也可以通过指定第二个模板类型参数来使用其他类型的容器：

```
std::queue<std::string, std::list<std::string>>words;
```

底层容器必须提供这些操作：front()、back()、push_back()、pop_front()、empty() 和 size()。

## queue 操作

queue 和 stack 有一些成员函数相似，但在一些情况下，工作方式有些不同：

*   front()：返回 queue 中第一个元素的引用。如果 queue 是常量，就返回一个常引用；如果 queue 为空，返回值是未定义的。
*   back()：返回 queue 中最后一个元素的引用。如果 queue 是常量，就返回一个常引用；如果 queue 为空，返回值是未定义的。
*   push(const T& obj)：在 queue 的尾部添加一个元素的副本。这是通过调用底层容器的成员函数 push_back() 来完成的。
*   push(T&& obj)：以移动的方式在 queue 的尾部添加元素。这是通过调用底层容器的具有右值引用参数的成员函数 push_back() 来完成的。
*   pop()：删除 queue 中的第一个元素。
*   size()：返回 queue 中元素的个数。
*   empty()：如果 queue 中没有元素的话，返回 true。
*   emplace()：用传给 emplace() 的参数调用 T 的构造函数，在 queue 的尾部生成对象。
*   swap(queue<T> &other_q)：将当前 queue 中的元素和参数 queue 中的元素交换。它们需要包含相同类型的元素。也可以调用全局函数模板 swap() 来完成同样的操作。

queue<T> 模板定义了拷贝和移动版的 operator=()，对于所保存元素类型相同的 queue 对象，它们有一整套的比较运算符，这些运算符的工作方式和 stack 容器相同。

和 stack 一样，queue 也没有迭代器。访问元素的唯一方式是遍历容器内容，并移除访问过的每一个元素。例如：

```
std::deque<double> values {1.5, 2.5, 3.5, 4.5}; std::queue<double> numbers(values);

while (!numbers, empty())
{
    std ::cout << numbers. front() << " "; // Output the 1st element 
    numbers. pop();  // Delete the 1st element
}
std::cout << std::endl;
```

用循环列出 numbers 的内容，循环由 empty() 返回的值控制。调用 empty() 可以保证我们能够调用一个空队列的 ftont() 函数。如代码所示，为了访问 queue 中的全部元素，必须删除它们。如果不想删除容器中的元素，必须将它们复制到另一个容器中。如果一定要这么操作，我们可能需要换一个容器。

## queue 容器的实际使用

这里汇集了一些使用 queue 容器的示例。这是一个用 queue 模拟超市运转的程序。结账队列的长度是超市运转的关键因素。它会影响超市可容纳的顾客数——因为太长的队伍会使顾客感到气馁，从而放弃排队。在很多情形中——医院可用病床数会严重影响应急处理设施的运转，也会产生同样的队列问题。我们的超市模拟是一个简单模型，灵活性有限。

可以在头文件 Customer.h 中定义一个类来模拟顾客：

```
// Defines a customer by their time to checkout
#ifndef CUSTOMER_H
#define CUSTOMER_H

class Customer
{
protected:
    size_t service_t {}; // Time to checkout
public:
    explicit Customer(size_t st = 10) :service_t {st}{}

    // Decrement time remaining to checkout
    Customer& time_decrement()
    {
        if (service_t > 0)
            --service_t;
        return *this;
    }
    bool done() const { return service_t == 0; }
};
#endif
```

这里只有一个成员变量 service_t，用来记录顾客结账需要的时间。每个顾客的结账时间都不同。每过一分钟，会调用一次 time_decrement() 函数，这个函数会减少 service_t 的值，它可以反映顾客结账所花费的时间。当 service_t 的值为 0 时，成员函数 done() 返回 true。

超市的每个结账柜台都有一队排队等待的顾客。Checkout.h 中定义的 Checkout 类如下：

```
// Supermarket checkout - maintains and processes customers in a queue
#ifndef CHECKOUT_H
#define CHECKOUT_H
#include <queue> // For queue container
#include "Customer.h"

class Checkout
{
private:
    std::queue<Customer> customers; // The queue waiting to checkout
public:
    void add(const Customer& customer) { customers.push(customer); }
    size_t qlength() const { return customers.size(); }

    // Increment the time by one minute
    void time_increment()
    {
        if (!customers.empty())
        { // There are customers waiting...
            if (customers.front().time_decrement().done())  // If the customer is done...
                customers.pop(); // ...remove from the queue
        }
    };

    bool operator<(const Checkout& other) const { return qlength() < other.qlength(); }
    bool operator>(const Checkout& other) const { return qlength() > other.qlength(); }
};
#endif
```

这相当于自我解释。queue 容器是 Checkout 唯一的成员变量，用来保存等待结账的 Customer 对象。成员函数 add() 可以向队列中添加新顾客。只能处理队列中的第一个元素。 每过一分钟，调用一次 Checkout 对象的成员函数 time_increment(}，它会调用第一个 Customer 对象的成员函数 time_decrement() 来减少剩余的服务时间，然后再调用成员函数 done()。如果 done() 返回 true，表明顾客结账完成，因此把他从队列中移除。Checkout 对象的比较运算符可以比较队列的长度。

为了模拟超市结账，我们需要有随机数生成的功能。因此打算使用 random 头文件中的一个非常简单的工具，但不打算深入解释它。我们会在教程后面的章节深入探讨 random 头文件中的内容。程序使用了一个 uniform_int_distribution() 类型的实例。顾名思义，它定义的整数值在最大值和最小值之间均匀分布。在均匀分布中，所有这个范围内的值都可能相等。可以在 10 和 100 之间定义如下分布：

```
std::uniform_int_distribution<> d {10, 100};
```

这里只定义了分布对象 d，它指定了整数值分布的范围。为了获取这个范围内的随机数，我们需要使用一个随机数生成器，然后把它作为参数传给 d 的调用运算符，从而返回一个随机整数。 random 头文件中定义了几种随机数生成器。这里我们使用最简单的一个，可以按如下方式定义：

```
std::random_device random_number_engine;
```

为了在 d 分布范围内生成随机数，我们可以这样写：

```
auto value = d(random_number_engine); // Calls operator()() for d
```

value 的值在 d 分布范围内。

完整模拟器的源文件如下：

```
// Simulating a supermarket with multiple checkouts
#include <iostream>                              // For standard streams
#include <iomanip>                               // For stream manipulators
#include <vector>                                // For vector container
#include <string>                                // For string class
#include <numeric>                               // For accumulate()
#include <algorithm>                             // For min_element & max_element
#include <random>                                // For random number generation
#include "Customer.h"
#include "Checkout.h"

using std::string;
using distribution = std::uniform_int_distribution<>;

// Output histogram of service times
void histogram(const std::vector<int>& v, int min)
{
    string bar (60, '*');                          // Row of asterisks for bar
    for (size_t i {}; i < v.size(); ++i)
    {
        std::cout << std::setw(3) << i+min << " "    // Service time is index + min
        << std::setw(4) << v[i] << " "             // Output no. of occurrences
        << bar.substr(0, v[i])                     // ...and that no. of asterisks
        << (v[i] > static_cast<int>(bar.size()) ? "...": "")
        << std::endl;
    }
}

int main()
{
    std::random_device random_n;

    // Setup minimum & maximum checkout periods - times in minutes
    int service_t_min {2}, service_t_max {15};
    distribution service_t_d {service_t_min, service_t_max};

    // Setup minimum & maximum number of customers at store opening
    int min_customers {15}, max_customers {20};
    distribution n_1st_customers_d {min_customers, max_customers};

    // Setup minimum & maximum intervals between customer arrivals
    int min_arr_interval {1}, max_arr_interval {5};
    distribution arrival_interval_d {min_arr_interval, max_arr_interval};

    size_t n_checkouts {};
    std::cout << "Enter the number of checkouts in the supermarket: ";
    std::cin >> n_checkouts;
    if (!n_checkouts)
    {
        std::cout << "Number of checkouts must be greater than 0\. Setting to 1." << std::endl;
        n_checkouts = 1;
    }

    std::vector<Checkout> checkouts {n_checkouts};
    std::vector<int> service_times(service_t_max-service_t_min+1);

    // Add customers waiting when store opens
    int count {n_1st_customers_d(random_n)};
    std::cout << "Customers waiting at store opening: " << count << std::endl;
    int added {};
    int service_t {};
    while (added++ < count)
    {
        service_t = service_t_d(random_n);
        std::min_element(std::begin(checkouts), std::end(checkouts))->add(Customer(service_t));
        ++service_times[service_t - service_t_min];
    }

    size_t time {};                                // Stores time elapsed
    const size_t total_time {600};                 // Duration of simulation - minutes
    size_t longest_q {};                           // Stores longest checkout queue length

    // Period until next customer arrives
    int new_cust_interval {arrival_interval_d(random_n)};

    // Run store simulation for period of total_time minutes
    while (time < total_time)                      // Simulation loops over time
    {
        ++time;                                      // Increment by 1 minute

        // New customer arrives when arrival interval is zero
        if (--new_cust_interval == 0)
        {
            service_t = service_t_d(random_n);         // Random customer service time
            std::min_element(std::begin(checkouts), std::end(checkouts))->add(Customer(service_t));
            ++service_times[service_t - service_t_min];  // Record service time

            // Update record of the longest queue occurring
            for (auto & checkout : checkouts)
                longest_q = std::max(longest_q, checkout.qlength());

            new_cust_interval = arrival_interval_d(random_n);
        }

        // Update the time in the checkouts - serving the 1st customer in each queue
        for (auto & checkout : checkouts)
            checkout.time_increment();
    }

    std::cout << "Maximum queue length = " << longest_q << std::endl;
    std::cout << "\nHistogram of service times:\n";
    histogram(service_times, service_t_min);

    std::cout << "\nTotal number of customers today: "
            << std::accumulate(std::begin(service_times), std::end(service_times), 0)
            << std::endl;
}
```

直接使用 using 指令可以减少代码输入，简化代码。顾客服务次数记录在 vector 中。服务时间减去 service_times 的最小值可以用来索引需要自增的 vector 元素，这导致 vector 的第一个元素会记录下最少服务时间的发生次数。histogram() 函数会以水平条形图的形式生成每个服务时间出现次数的柱状图。

输入的唯一数字是 checkouts。此处选择将模拟持续时间设置为 600 分钟，也可以用参数输入这个时间。main() 函数生成了顾客服务时间，超市开门时等在门外的顾客数，以及顾客到达时间间隔的分布对象。它表明顾客在同一时间到达。我们可以轻松地将这个程序扩展为每次到达的顾客数是一个处于一定范围内的随机数。

顾客总是可以被分配到最短的结账队列。通过调用 min_elemeiit() 算法可以找到最短的 Checkout 对象队列。这会使用<运算符比较元素，但是这个算法的另一个版本有第三个参数可以指定比较函数。在这次模拟开始前，当超市开门营业时，在门外等待的顾客的初始 序列被添加到 Checkout 对象中，然后服务时间记录被更新。

模拟在 while 循环中进行。在每次循环中，time 都会增加 1 分钟。在下一个顾客到达期间，new_cust_interval 会在每次循环中减小，直到等于 0。用新的随机服务时间生成新的顾客，然后把它加到最短的 Checkout 对象队列中。这个时候也会更新变量 longest_q，因为在添加新顾客后，可能出现新的最长队列。然后调用每个 Checkout 对象的 time_increment() 函数来处理队列中的第一个顾客。

下面是一些示例输出：

Enter the number of checkouts in the supermarket: 3
Customers waiting at store opening: 19
Maximum queue length = 10

Histogram of service times:
  2   16 ****************
  3   15 ***************
  4   12 ************
  5   11 ***********
  6   17 *****************
  7   20 ********************
  8   18 ******************
  9   16 ****************
10   10 **********
11   12 ************
12   16 ****************
13   21 *********************
14   17 *****************
15   21 *********************

Total number of customers today: 222

这里有 3 个结账柜台。将它们设为 2 个时，最长队列的长度达到 42——已经长到会让顾客放弃付款。还可以做更多改进，让模拟更加真实。均匀分配并不符合实际，例如，顾客通常成群结队到来。可以增加一些其他的因素，比如收银员休息时间、某个收银员生病工作状态不佳，这些都会导致顾客不选择这个柜台结账。