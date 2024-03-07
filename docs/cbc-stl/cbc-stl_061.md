# C++ unordered_map 获取（访问）元素详解

对于 unordered_map，可以在下标运算符中使用键来获取它所对应对象的引用。例如：

```
people["Jim"] = 22; //Set Jim's age to 22;
people["May"] = people["Jim"]; //Set May's age to Jim's
++people ["Joe"] ; //Increment Joe's age
people ["Kit"] = people ["Joe"]; // Set Kit's age to Joe's
```

这和 map 容器的操作是一样的。在下标中使用不存在的键时，会以这个键为新键生成一个新的元素，新元素中对象的值是默认的。如果容器中不存在”Kit”，上面最后一条语句会生成一个以“kit”为键、年龄值为 0 的元素；最后"Joe”所关联的对象会被复制到"Kit"。

成员函数 at() 会返回参数所关联对象的引用，如果键不存在，会拋出一个 out_of_range 异常。所以当我们不想生成有默认对象的元素时，应该选择使用 at() 而不是下标运算符。你可能已经发现成员函数 find() 和 equal_range() 的工作方式和之前描述的 map 是一样的。

unordered_map 的迭代器是可以使用的，因此可以用基于范围的 for 循环来访问它的元 素，例如：

```
for(const auto& person : people)
    std::cout << person.first << " is "<< person.second <<std::endl;
```

这样就可以列出 people 容器中的全部元素。

## 访问格子

可以访问 unordered_map 的个别格子及其包含的元素。可以用这个容器的成员函数 begin() 和 end() 的重载版来做到这一点，它们可以返回容器元素的迭代器。格子的索引从 0 开始，可以通过将索引值传给容器的成员函数 begin() 来获取给定位置的格子中第一个元素的迭代器。例如：

```
auto iter = people.begin(1); // Returns an iterator for the 2nd bucket
```

将索引值传给容器的成员函数 cbegin() 会返回一个 const 迭代器，它指向位于索引位置的格子中的第一个元素。这个容器的成员函数 end() 和 cend() 也有这样的版本，它们接受一个索引值，分别返回一个迭代器和一个 const 迭代器，它们指向位于指定位置的格子中的最后一个元素的下一个位置。可以输出特定格子中的元素，也就是说，需要使用循环：

```
size_t index{1};
std::cout <<"The elements in bucket["<< index << "] are:\n";
for(auto iter = people.begin(index); iter != people.end(index); ++iter)
    std::cout <<iter->first << " is " <<iter->second <<std::endl;
```

我们已经看到 unordered_map 的成员函数 bucket_count() 返回的格子个数。bucket_size() 可以返回参数指定的格子中的元素个数。bucket() 返回的是格子的索引值，包含和传入的参数键匹配的元素。可以用不同的方式来组合使用它们。例如：

```
string key {"May"};
if(people.find(key) != std::end(people))
    std::cout << "The number of elements in the bucket containing " << key << " is "<< people.bucket_size(people.bucket(key)) << std:: endl;
```

bucket_size() 的参数是 bucket() 返回的索引值。当键在容器中时，这段代码才会执行输语句。输出记录了包含键的格子中的元素个数。下面有一个示例，可以让我们深入了解在添加元素时，我们系统中的 unorderect_map 是如何做的：

```
// Analyzing how and when the number of buckets in an unordered_map container increases
#include <iostream>                              // For standard streams
#include <iomanip>                               // For stream manipulators
#include <string>                                // For string class
#include <unordered_map>                         // For unordered_map container
#include <vector>                                // For vector container
#include <algorithm>                             // For max_element() algorithm

using std::string;
using std::unordered_map;

// Outputs number of elements in each bucket
void list_bucket_counts(const std::vector<size_t>& counts)
{
    for(size_t i {}; i < counts.size(); ++i)
    {
        std::cout << "bucket[" << std::setw(2) << i << "] = " << counts[i] << "  ";
        if((i + 1) % 6 == 0) std::cout << '\n';
    }
    std::cout << std::endl;
}

int main()
{
    unordered_map<string, size_t> people;
    float mlf {people.max_load_factor()};                         // Current maximum load factor
    size_t n_buckets {people.bucket_count()};                     // Number of buckets in container

    std::vector<size_t> bucket_counts (people.bucket_count());    // Records number of elements per bucket
    string name {"Name"};                                         // Key - with value appended
    size_t value {1};                                             // Element value
    size_t max_count {8192};                                      // Maximum number of elements to insert
    auto lf = people.load_factor();                               // Current load factor
    bool rehash {false};                                          // Records when rehash occurs

    while(mlf <= 1.5f)                                            // Loop until max load factor is 1.5
    {
        std::cout << "\n\n***************New Container***************"
              << "\nNumber of buckets: " << n_buckets << "  Maximum load factor: " << mlf << std::endl;
        // Insert max elements in container
        for(size_t n_elements {}; n_elements < max_count; ++n_elements)
        {
            lf = people.load_factor();                               // Record load factor
            people.emplace("name" + std::to_string(++value), value);
            auto new_count = people.bucket_count();                  // Current bucket count
            if(new_count > n_buckets)                                // If bucket count increases...
            {                                                        // Output info
                std::cout << "\nBucket count increased to " << new_count<< ". Load factor was " << lf << " and is now " << people.load_factor()<< "\nMaximum elements in a bucket was "<< *std::max_element(std::begin(bucket_counts), std::end(bucket_counts)) << std::endl;
                if(n_buckets <= 64)
                {
                    std::cout << "Bucket counts before increase were: " << std::endl;
                    list_bucket_counts(bucket_counts);
                }
                n_buckets = new_count;                                  // Update bucket count
                bucket_counts = std::vector < size_t > (n_buckets);     // New vector for counts
                rehash = true;                                          // Record rehash occurred
            }

            // Record current bucket counts
            for(size_t i {}; i < n_buckets; ++i)
                bucket_counts[i] = people.bucket_size(i);

            if(rehash)                                                // If the container was rehashed...
            {                                                         // ...output info
                rehash = false;                                         // Reset rehash indicator
                std::cout << "\nRehashed container. Bucket count is " << n_buckets << ". Element count is " << people.size()<< "\nMaximum element count in a bucket is now "<< *std::max_element(std::begin(bucket_counts), std::end(bucket_counts)) << std::endl;
                if(n_buckets <= 64)                                     // If no more than 64 buckets...
                {
                    std::cout << "\nBucket counts after rehash are:\n";
                    list_bucket_counts(bucket_counts);
                }
            }
        }
        std::cout << "Final state for this container is:\n"<< "Bucket count: " << people.bucket_count() << "  Element count: " << people.size()<< "  Maximum element count in a bucket: "<< *std::max_element(std::begin(bucket_counts), std::end(bucket_counts)) << std::endl;
        value = 1;                                                  // Reset key suffix
        people = unordered_map<string, size_t>();                   // New empty container
        n_buckets = people.bucket_count();
        bucket_counts = std::vector < size_t >(n_buckets);          // New vector for bucket counts
        mlf += 0.25f;                                               // Increase max load factor...
        people.max_load_factor(mlf);                                // ...and set for container
    }
}
```

上述程序会记录元素个数增加时的载入因子和格子个数。通过这种方式，可以了解容器在什么条件下是以何种方式增加格子的。因为这段代码的执行会花费相当长的时间，所以我们需要耐心等待一下。如果觉得花费的时间太长了，可以减小变量 max 的值。

这个示例以一个空的 unordered_map 容器开始，然后插入 max 所指定的有限个新元素。然后通过在参数“name”的后面追加 value 递增后 to_string() 返回的字符串来构造唯一的键。 to_string() 函数定义在 string 头文件中，可以将任意数值类型转换为 string 对象。

每个格子中的元素个数记录在一个 vector 容器中。外层的 while 循环会一直进行下去，只要最大载入因子小于等于 1.5。嵌套的 for 循环会向 unordered_map 容器中插入 max_count 个元素。无论什么时候格子个数改变，都会调用辅助函数 list_bucket_counts() 来输出每个格子中的元素个数。

为了避免大量输出导致无法管理，只输出 64 个或更少的格子。当插入 max_count 个元素后，会使用更大的载入因子来生成 unordered_map，对这个新容器继续重复内会循环。这样就展示了最大载入因子如何影响格子个数的增加程度，从而导致元素被重新哈希。

对于运行结果，由于运行过程会占用太多的空间，我们可以简单概括下发生了什么。默认的格子数是 8。在添加了 8 个元素后，格子数从 8 增加到了 64，这是一个非常大的变化。当格子的最大元素个数是 2，但只有一个格子包含两个元素时；元素的总个数是 9。输出显示当载入因子接近 1.0 时，会触发格子个数的增加，格子个数下一次会乘以因子 8，从 64 到 512 (因运行系统而异)。格子个数的因子会缓慢增加到 2，所以格子个数的序列是 8、64、512、1024、2048、4096、8192。随着格子个数的增加，观察有多少空格子是很有意思的。

在我自己的系统上，所有格子的最大元素数目是 8，一点儿也不令人惊讶的是它也是最大的载入因子。在载入因子最大为 1.5 时，从一个格子中得到了 7 个元素。每次格子个数的增加都会导致容器中的所有元素被重新哈希分配到新的位置。可以很容易地对程序做一下调整，使它能够输出在格子增加前后的元素，这样就能够知道元素是如何被移动的。这显然会涉及相当大的开销，所以在保存的元素增加时，开始得到正确的格子数就变得更重要。

从系统上的输出可以了解到容器是如何将原始哈希值映射到格子的索引上的。格子数总是为 2 的幂。这样就允许格子的索引是来自于原始哈希值的固定位序列：8 个格子需要 3 位，64 个需要 6 位，512 个需要 9 位，以此类推。这就使获取格子的索引变得简单和快速。也就解释了为什么在格子数增加后，需要重新对元素进行哈希。从给定的哈希值中取 6 位要比取 3 位更能表示不同的索引值，所以一个给定的原始哈希值可能会被映射到不同的格子中。