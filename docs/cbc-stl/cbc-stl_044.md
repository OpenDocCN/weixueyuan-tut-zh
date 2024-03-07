# C++序列容器存储智能指针详解

通常用容器保存指针比保存对象更好，而且大多数时候，保存智能指针比原生指针好。下面是一些原因：

*   在容器中保存指针需要复制指针而不是它所指向的对象。复制指针通常比复制对象快。
*   在容器中保存指针可以得到多态性。存放元素基类指针的容器也可以保存其派生类型的指针。当要处理有共同基类的任意对象序列时，这种功能是非常有用的。应用这一特性的一个常见示例是展示一个含有直线、曲线和几何形状的对象序列。
*   对指针容器的内容进行排序的速度要比对对象排序快；因为只需要移动指针，不需要移动对象。
*   保存智能指针要比保存原生指针安全，因为在对象不再被引用时，自由存储区的对象会被自动删除。这样就不会产生内存泄漏。不指向任何对象的指针默认为 nullptr。

如你所知，主要有两种类型的智能指针：unique_ptr<T> 和 shared_ptr<T>，其中 unique_ptr<T> 独占它所指向对象的所有权，而 shared_ptr<T> 允许多个指针指向同一个对象。还有 weak_ptr<T> 类型，它是一类从 shared_ptr<T> 生成的智能指针，可以避免使用 shared_ptrs<T> 带来的循环引用问题。unique_ptr<T> 类型的指针可以通过移动的方式保存到容器中。例如，下面的代码可以通过编译：

```
std::vector<std::unique_ptr<std::string>> words;
words.push_back(std::make_unique<std::string>("one"));
words.push_back(std::make_unique<std::string>("two"));
```

vector 保存了 unique_ptr<string> 类型的智能指针。make_unique<T>() 函数可以生成对象和智能指针，并且返回后者。因为返回结果是一个临时 unique_ptr<string> 对象，这里调用一个有右值引用参数的 push_back() 函数，因此不需要拷贝对象。另一种添加 unique_ptr 对象的方法是，先创建一个局部变量 unique_ptr ，然后使用 std::move() 将它移到容器中。然而，后面任何关于拷贝容器元素的操作都会失败，因为只能有一个 unique_ptr 对象。如果想能够复制元素，需要使用 shared_ptr 对象；否则就使用 unique_ptr 对象。

## 在序列容器中保存指针

下面首先解释一些在容器中使用原生指针会碰到的问题，然后再使用智能指针（这是推荐的使用方式）。下面是一段代码，用来从标准输入流读取单词，然后将指向自由存储区的字符串对象的指针保存到 vector 容器中：

```
std::vector<std::string*> words;
std::string word;
std::cout << "Enter words separated by spaces, enter Ctrl+Z on a separate line to end: \n";
while (true)
{
    if ((std::cin >> word).eof())
    {
        std::cin. clear();
        break;
    }

    words.push_back(new std::string {word});// Create object and store its address
}
```

push_back() 的参数表达式在自由存储区生成了一个字符串对象，因此 push_back() 的参数是一个对象的地址。可以按如下方式输出 words 中的内容：

```
for (auto& w : words)
    std: : cout << w <<" ";
std::cout << std::endl;
```

如果想使用迭代器来访问容器中的元素，输出字符串的代码可以这样写：

```
for (auto iter = std::begin(words);iter != std::end(words); ++iter)
    std::cout << **iter <<" ";
std::cout << std::endl;
```

iter 是一个迭代器，必须通过解引用来访问它所指向的元素。这里，容器的元素也是指针，因此必须解引用来获取 string 对象。因此表达式为：**iter。注意，在删除元素时，需要先释放它所指向的内存。如果不这样做，在删除指针后，就无法释放它所指向的内存，除非保存了指针的副本。这是容器中的原生指针常见的内存泄漏来源。下面演示它如何在 words 中发生：

```
for (auto iter = std::begin(words);iter != std::end(words);)
{
    if (**iter == "one")
        words.erase (iter); // Memory leak!
    else
        ++iter;
}
```

这里删除了一个指针，但它所指向的内存仍然存在。无论什么时候删除一个是原生指针的元素，都需要首先释放它所指向的内存：

```
for (auto iter = std::begin(words); iter != std::end(words);)
{
    if (**iter == "one")
    {
        delete *iter;//Release the memory...
        words.erase (iter);    //... then delete the pointer
    }
    else
        ++iter;
}
```

在离开 vector 的使用范围之前，记住要删除自由存储区的 string 对象。可以按如下方式来实现：

```
for (auto& w : words)
    delete w; // Delete the string pointed to
words.clear(); // Delete all the elements from the vector
```

用索引来访问指针，这样就可以使用 delete 运算符删除 string 对象。当循环结束时，vector 中的所有指针元素都会失效，因此不要让 vector 处于这种状态。调用 dear() 移除所有元素，这样 size() 会返回 0。当然，也可以像下面这样使用迭代器：

```
for (auto iter = std::begin(words);iter != std::end(words); ++iter)
    delete *iter;
```

如果保存了智能指针，就不用担心要去释放自由存储区的内存。智能指针会做这些事情。下面是一个读入字符串，然后把 shared_ptr<string> 保存到 vector 中的代码片段：

```
std::vector<std::shared_ptr<std::string>> words; std::string word;
std::cout << "Enter words separated by spaces, enter Ctrl+Z on a separate line to end:\n";
while (true)
{
    if ((std::cin >> word).eof())
    {
        std::cin. clear ();
        break;
    }
    words.push_back(std::make_shared<string>(word)); // Create smart pointer to string & store it
}
```

这和使用原生指针的版本没有什么不同。vector 模板现在的类型参数是 std::shared_ptr<std::string>，push_back() 的参数会调用 make_shared()，在自由存储区生成 string 对象和一个指向它的智能指针。因为智能指针由参数表达式生成，这里会调用一个右值引用参数版的 push_back() 来将指针移到容器中。

模板类型参数可能有些冗长，但是可以使用 using 来简化代码。例如：

```
using PString = std::shared_ptr<std::string>;
```

使用 using 后，可以这样定义：

```
std::vector<PString> words;
```

可以通过智能指针元素来访问字符串，这和使用原生指针相同。前面那些输出 words 内容的代码片段都可以使用智能指针。当然，不需要删除自由存储区的 string 对象；因为智能指针会做这些事情。执行 words.clear() 会移除全部的元素，因此会调用智能指针的析构函数；这也会导致智能指针释放它们所指向对象的内存。

为了阻止 vector 太频繁地分配额外内存，可以先创建 vector，然后调用 reserve() 来分配一定数量的初始内存。例如：

```
std::vector<std::shared_ptr<std::>>words;
words.reserve(100); // Space for 100 smart pointers
```

这样生成 vector 比指定元素个数来生成要好，因为每一个元素都是通过调用 shared_ptr<string> 构造函数生成的。不这样做也不是什么大问题，但会产生一些不必要的额外开销，即使开销很小。通常，每个智能指针所需要的空间远小于它们所指向对象需要的空间，因此可以大方地使用 reserve() 来分配空间。

可以在外面使用保存的 shared_ptr<T> 对象的副本。如果不需要这种功能，应该使用 unique_ptr<T> 对象。下面展示如何在 words 中这样使用：

```
std::vector<std::unique_ptr<std::string>>words;
std::string word;
std::cout << "Enter words separated by spaces, enter Ctrl+Z on a separate line to end:\n";
while (true)
{
    if ((std::cin >> word).eof())
    {
        std::cin.clear();
        break;
    }
    words.push_back(std::make_unique<string>(word));
    //Create smart pointer to string & store it
}
```

在上面的代码中，用 unique 代替 shared 是没有差别的。

我们看一下，如何使用智能指针来实现前面章节中的超市结账模拟程序。 Customer 类的定义和之前的版本相同，但是 Checkout 类的定义中使用了智能指针，因而产生了一些变化，我们也可以在 main() 中使用智能指针。在整个程序中，我们都不需要使用智能指针的副本，因此我们选择使用 unique_ptr<T>。下面是 Checkout.h 头文件中的新内容：

```
// Supermarket checkout - using smart pointers to customers in a queue
#ifndef CHECKOUT_H
#define CHECKOUT_H
#include <queue> // For queue container
#include <memory> // For smart pointers
#include "Customer.h"
using PCustomer = std::unique_ptr<Customer>;

class Checkout
{
private:
    std::queue<PCustomer> customers;                // The queue waiting to checkout

public:
    void add(PCustomer&& customer) { customers.push(std::move(customer)); }
    size_t qlength() const { return customers.size(); }

    // Increment the time by one minute
    void time_increment()
    {
        if (customers.front()->time_decrement().done())  // If the customer is done...
        customers.pop();                               // ...remove from the queue
    };

    bool operator<(const Checkout& other) const { return qlength() < other.qlength(); }
    bool operator>(const Checkout& other) const { return qlength() < other.qlength(); }
};
#endif
```

我们需要直接包含 memory 头文件，这样就可以使用智能指针类型的模板。queue 容器保存 PCustomer 元素，用来记录排队结账的顾客。使用 using 为 std::unique_ptr<Customer> 定义了一个别名 PCustomer，这可以节省大量的输入。PCustomer 对象不能被复制，因而当调用 add() 函数时，它的参数是右值引用，参数会被移到容器中。以 unique 指针作为元素时，也会以同样的方式被移到容器中；当然，参数不能是 const。做了这些修改后，就可以使用 unique_ptr 了，不再需要修改其他的内容。

```
// Using smart pointer to simulate supermarket checkouts
#include <iostream>                              // For standard streams
#include <iomanip>                               // For stream manipulators
#include <vector>                                // For vector container
#include <string>                                // For string class
#include <numeric>                               // For accumulate()
#include <algorithm>                             // For min_element & max_element
#include <random>                                // For random number generation
#include <memory>                                // For smart pointers
#include "Checkout.h"
#include "Customer.h"

using std::string;
using distribution = std::uniform_int_distribution<>;
using PCheckout = std::unique_ptr<Checkout>;

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
    std::uniform_int_distribution<> service_t_d {service_t_min, service_t_max};

    // Setup minimum & maximum number of customers at store opening
    int min_customers {15}, max_customers {20};
    distribution n_1st_customers_d {min_customers, max_customers};

    // Setup minimum & maximum intervals between customer arrivals
    int min_arr_interval {1}, max_arr_interval {5};
    distribution arrival_interval_d {min_arr_interval, max_arr_interval};

    size_t n_checkouts {};
    std::cout << "Enter the number of checkouts in the supermarket: ";
    std::cin >> n_checkouts;
    if(!n_checkouts)
    {
        std::cout << "Number of checkouts must be greater than 0\. Setting to 1." << std::endl;
        n_checkouts = 1;
    }

    std::vector<PCheckout> checkouts;
    checkouts.reserve(n_checkouts);                // Reserve memory for pointers

    // Create the checkouts
    for (size_t i {}; i < n_checkouts; ++i)
        checkouts.push_back(std::make_unique<Checkout>());
    std::vector<int> service_times(service_t_max-service_t_min+1);

    // Add customers waiting when store opens
    int count {n_1st_customers_d(random_n)};
    std::cout << "Customers waiting at store opening: " << count << std::endl;
    int added {};
    int service_t {};

    // Define comparison lambda for pointers to checkouts
    auto comp = [](const PCheckout& pc1, const PCheckout& pc2){ return *pc1 < *pc2; };
    while (added++ < count)
    {
        service_t = service_t_d(random_n);
        auto iter = std::min_element(std::begin(checkouts), std::end(checkouts), comp);
        (*iter)->add(std::make_unique<Customer>(service_t));
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
            (*std::min_element(std::begin(checkouts), std::end(checkouts), comp))->add(std::make_unique<Customer>(service_t));
            ++service_times[service_t - service_t_min];  // Record service time

            // Update record of the longest queue length
            for (auto& pcheckout : checkouts)
                longest_q = std::max(longest_q, pcheckout->qlength());

            new_cust_interval = arrival_interval_d(random_n);
        }

        // Update the time in the checkouts - serving the 1st customer in each queue
        for (auto& pcheckout : checkouts)
            pcheckout->time_increment();
    }

    std::cout << "Maximum queue length = " << longest_q << std::endl;
    std::cout << "\nHistogram of service times:\n";
    histogram(service_times, service_t_min);

    std::cout << "\nTotal number of customers today: "
                << std::accumulate(std::begin(service_times), std::end(service_times), 0)
                << std::endl;
}
```

vector 容器现在保存的是指向 Checkout 对象的 unique 指针。vector 的迭代器指向 Checkout 对象，即 unique_ptr<Checkout> 对象的指针，因而可以通过迭代器来调用 Checkout 的成员函数。首先必须解引用迭代器，然后用间接成员选择运算符来调用函数。可以看到，我们已经修改了 main() 中的相关代码。min_element() 默认使用 < 运算符来从迭代器指向的元素中获取结果。默认会比较智能指针，但是并不能得到正确的结果。我们需要为 min_element() 提供第 3 个参数作为它所使用的比较函数。这个函数是由名为 comp 的 lambda 表达式定义的。因为我们想在后面继续使用这个表达式，所以对它做命名。

为了访问 Checkout 对象，这个 lambda 表达式解引用了智能指针参数，然后使用 Checkout 类的成员函数 operator<() 来比较它们。所有的 Checkout 和 Customer 对象都是在自由储存区生成的。智能指针会维护它们所使用的内存。这个版本的模拟程序的输出和之前版本的相同。在这个示例中也可以使用 shared_ptr<T>，但是它们会执行得慢一些。就执行时间和内存使用而言， unique_ptr<T> 对象相对于原生指针的开销最小。