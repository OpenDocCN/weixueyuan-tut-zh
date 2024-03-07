# C++ unordered_multimap 用法详解

unordered_multimap 是一个允许有重复键的无序 map。因此，它支持的操作实际上和 unordered_map 容器是相同的，为了处理多个重复键所做的添加和更改除外。后面会对这些差别做些讨论。生成 unordered_multimap 的方式和 unordered_map 相同。例如：

```
std:: unordered_multimap<std::string,size_t> people {{"Jim",33}, {"Joe",99}};
```

可以使用 insert()、emplace()、emplace_hint() 来添加新元素，这和 unordered_multimap 相同，只要参数和容器中的元素类型一致。这些成员函数都会返回一个指向容器中新元素的迭代器；在使用 inserted emplace() 的情况下，unordered_multimap 和 unordered_map 有些不同，unordered_multimap 的这两个函数会返回一个 pair 对象，它用来说明插入是否成功。如果不成功，也是一个迭代器。例如： 

```
auto iter = people.emplace("Jan", 45);
people.insert({"Jan", 44});
people.emplace_hint(iter, "Jan", 46);
```

第 3 条语句使用了第 1 条语句返回的迭代器作为插入元素的提示符。这个提示符有时会被容器或我们的实现所忽略。

unordered_map 支持的成员函数 at() 和 operator[]() 对于 unordered_multimap 来说并不可用，因为潜在的重复键。唯一的选择是使用 find() 和 equal_range() 来访问元素。find() 总会返回它所找到的第一个元素的迭代器，如果找不到这个键，会返回一个结束迭代器。可以以键为参数调用 count() 来发现容器中给定键的元素个数。下面展示实际用法：

```
std:: string key{"Jan"};
auto n = people.count(key);//Number of elements stored with key
if(n == 1)
    std::cout << key << " is " << people.find(key)->second<<std::endl;
else if (n > 1)
{
    auto pr = people.equal_range (key); // pair of begin & end iterators returned
    while(pr.first != pr.second)
    {
        std::cout << key << " is " << pr.first->second << std::endl;
        ++pr.first; // Increment begin iterator
    }
}
```

当容器中只有一个 key 时，可以用 find() 来访问这个元素。如果超过一个，可以用 equal_range() 来访问这段元素。当然，在这两种情况下都可以使用 equal_range()。

让我们来看一个使用 unordered_multimap 的示例。在这个示例中也会展示一些定义函数模板来使用容器的方式。这个程序会实现一个电话簿，可以用姓名或名称来查找电话号码。此处用一个 pair 对象来封装名和姓，用一个元组来记录区号、交换码、电话号码，它们都是以 string 对象的形式保存的。使用下面的 using 简化代码：

```
using std::string;
using std::unordered_multimap;
using Name = std::pair<string, string>；
using Phone = std::tuple<string, string, string>;
```

电话号码可以用三个整数来表示，但这样组合起来更像编码而不是号码。电话号码中的每个元素都有固定个数的数字，有一些位数的组合是不允许的。如果想增加对号码检查的功能，显然用 string 对象可以使号码位数或区号的检查更简单。这里并没有包含这个功能，因为这会使本书的编码量大大增加。

为了从 istream 对象读取电话号码，对 >> 运算符进行了重载。函数如下：

```
inline std::istream& operator>>(std::istream& in, Phones phone)
{
    string area_code {},exchange {}, number {};
    in >> std::ws >> area_code >> exchange >> number; phone = std::make_tuple(area_code, exchange, number); return in;
}
```

phone 是元组模板类型。这里 make_tuple() 使用从 in 读出的局部变量的值生成了一个 phone 对象。

我们可以为 Name 对象做相同的事：

```
inline std::istream& operator>>(std::istream& in, Names& name)
{
    in >> std::ws >> name.first >> name.second;
    return in;
}
```

这里会丢弃从 in 读入的任何前置空格，然后读出两个 name 字符串作为 pair 对象的成员。当然，我们也需要输出功能。这里定义了 operator<<() 来为 phone 对象提供输出：

```
inline std::ostream& operator<<(std::ostream& out, const Phone& phone)
{
    std::string area_code {}, exchange {}, number {}; std::tie(area_codeA exchange, number) = phone;
    out << area_code <<" "<< exchange << " " << number; return out;
}
```

这里使用函数模板 tie<>() 生了一个包含三个局部变量引用的元组。然后为了将 phone 的成员变量的值保存到局部变量中，将 phone 赋值给 tie<>() 生成的元组，后面会输出这些局部变量。或者，使用函数模板 get<>() 来访问 phone 成员变量的值。这种方法显然更好，因为能够避免上面出现的 string 对象的副本，但这里是为了展示 tie<>() 函数的使用。

为 Name 对象重载 << 是很简单的：

```
inline std::ostream& operator<<(std::ostream& out, const Name& name)
{
    out << name.first << " " << name.second;
    return out;
}
```

所有的这些 I/O 函数都是内联的，所以把它们放在叫作 Record_IO.h 的头文件中。文件的开头是 #include 和 using:

```
#include <string>   //  For string class
#include <istream>  //  For istream class
#include <ostream>  //  For ostream class
#include <utility>  //  For pair type
#include <tuple>    //  For tuple type
using Name = std::pair <std::string, std::string>;
using Phone = std::tuple <std::string, std::string,std::string>;
```

这个程序会使用两个关联容器一其中一个以名称作为键，另一个以电话号码为键，所以它们都会包含相同的基本信息，但它们的访问方式不同。当然有更有效率的方式来做这些，但这里我们是为了尝试 unordered_multimap 容器的使用。main() 中定义的容器如下：

```
unordered_multimap<Name, Phone, NameHash> by_name {8, NameHash()};
unordered_multimap<Phone, Name, PhoneHash> by_number {8, PhoneHash()};
```

这里并没有为 pair 或 tuple 对象提供默认的哈希能力，所以我们不得不自己定义它们。 这里它们分别是 NameHash 和 PhoneHash 类型的函数对象。构造函数中哈希函数对象之前的参数是格子的个数，所以这里的格子数是指定的。它们等于系统的默认值。

把这两个哈希函数类型的定义放在同一个头文件中，名为 Hash_Function_Objects.h，文件的开头是下面这些代码：

```
#include <string>                                // For string class
#include <utility>                               // For pair type
#include <tuple>                                 // For tuple type

using Name = std::pair < std::string, std::string >;
using Phone = std::tuple < std::string, std::string, std::string >;
```

按如下方式定义 PhoneHash 类型：

```
// Hash a phone number
class PhoneHash
{
public:
    size_t operator()(const Phone& phone) const
    {
        return std::hash<std::string>()(std::get<0>(phone)+std::get<1>(phone)+std::get<2>(phone));
    }
};
```

通过用定义在 string 头文件中的 hash<string>() 模板的特例化来处理电话号码中三个元素串联后的结果，对应地生成哈希值。下面以相似的方式定义了 HashName 类型：

```
// Hash a name
class NameHash
{
public:
    size_t operator()(const Name& name) const
    {
        return std::hash<std::string>()(name.first + name.second);
    }
};
```

将输出操作打包放在单独的函数中：

```
void show_operations()
{
    std::cout << "Operations:\n"<< "A: Add an element.\n"<< "D: Delete elements.\n"<< "F: Find elements.\n"<< "L: List all elements.\n"<< "Q: Quit the program.\n\n";
}
```

通过名称或号码列出所有的元素。我们定义了一个函数模板来处理这两种可能：

```
template<typename Container>
void list_elements(const Container& container)
{
    for(const auto& element : container)
        std::cout << element.first << "  " << element.second << std::endl;
}
```

当函数被调用时，这个模板会从使用的参数中推断出容器的类型。两个容器包含的元素是 pair 对象。by_name 容器包含的是 pair<Name，Phone>S 象类型的元素，by_number 容器包含的是 pair<Phone，Name> 对象类型的元素。因为我们己经为 Name 和 Phone 类型重载了 operator<<()，循环体会为 pair 元素的成员变量类型自动选择合适的函数输出。把这个函数模板和 My_Templates.h 头文件后面的模板放在完整的示例中。

通过名称或号码来查找元素的过程本质上是相同的，所以我们也可以为它们定义一个函数模板：

```
template<typename Container>
auto find_elements(const Container& container)
{
    typename Container::key_type key {};
    std::cin >> key;
    auto pr = container.equal_range(key);
    return pr;
}
```

这段代码对应于 C++11 标准。返回类型是依赖容器类型的 pair<> 模板类型。这是因为返回的 pair 封装的迭代器类型是特定的容器类型。这就意味着编译器不能处理函数名之前的返回类型，因为容器的类型取决于函数参数的类型，这个类型稍后才用得到。

为了能够使 C++11 编译器可以确定返回类型，必须使用尾部返回类型语法。这会允许编译器在处理完函数参数后再处理返回类型。注意，typename 关键字对模板类型的规范是至关重要的，它对于局部变量 key 的规范也至关重要。容器中 key 的类型是由容器类的成员变量 key_type 指定的，所以 key 的类型规范会自动从容器中选择正确的类型。如果需要和键关联的对象的类型，它是由成员 Container:mapped_type 指定的，Container::value_type 指定了容器元素的类型。

C++14 标准为编译器引入了推断函数返回类型的能力，因而函数模板可以这样写：

```
template<typename Container>
auto find_elements(const Container& container) -> std::pair<typename Container::const_iterator, typename Container::const_iterator>
{
    typename Container::key_type key {};
    std::cin >> key;
    auto pr = container.equal_range(key);
    return pr;
}
```

这里不需要尾部返回类型，因为编译器可以从返回值 pr 中推断出返回类型。

这个操作允许通过搜索名称或号码来查找元素，每种情况都会返回一个 pair 对象，它包含的迭代器定义了一种或另一种类型元素的范围。我们可以另外定义一个函数模板来输出这段元素：

```
template<typename T>
void list_range(const T& pr)
{
    if(pr.first != pr.second)
    {
        for(auto iter = pr.first; iter != pr.second; ++iter)
            std::cout << "  " << iter->first << "  " << iter->second << std::endl;
    }
    else
        std::cout << "No records found.\n";
}
```

如果作为参数的 pair 对象的成员变量是相同的，那么范围为空，这种情况下我们只会输出一条消息。为 Name 和 Phone 类型实现的 << 运算符函数使这个模板可以正常使用。pair 成员变量的实际类型会自动选择合适的 operatorr<< 函数。需要注意的是，在已编译的代码中，这些模板不会减少代码量。它们只是提供了一种生成所使用函数的转换机制，提供了如何使用模板的简单演示。

main() 函数包含下面这些代码：

```
#include <iostream>                              // For standard streams
#include <cctype>                                 // For toupper()
#include <string>                                // For string class
#include <unordered_map>                         // For unordered_map container

#include "Record_IO.h"
#include "My_Templates.h"
#include "Hash_Function_Objects.h"

using std::string;
using std::unordered_multimap;
using Name = std::pair < string, string >;
using Phone = std::tuple < string, string, string >;

// Display command prompt
void show_operations()
{
    std::cout << "Operations:\n"<< "A: Add an element.\n"<< "D: Delete elements.\n"<< "F: Find elements.\n"<< "L: List all elements.\n"<< "Q: Quit the program.\n\n";
}

int main()
{
    unordered_multimap<Name, Phone, NameHash> by_name {8, NameHash()};
    unordered_multimap<Phone, Name, PhoneHash> by_number {8, PhoneHash()};
    show_operations();

    char choice {};                                     // Operation selection
    Phone number {};                                    // Records a number
    Name name {};                                       // Records a name

    while(std::toupper(choice) != 'Q')                  // Go until you quit...
    {
        std::cout << "Enter a command: ";
        std::cin >> choice;
        switch(std::toupper(choice))
        {
            case 'A':                                         // Add a record
                std::cout << "Enter first & second names, area code, exchange, and number separated by spaces:\n";
                std::cin >> name >> number;
                by_name.emplace(name, number);                  // Create in place...
                by_number.emplace(number, name);                // ...in both containers
                break;
            case 'D':                                         // Delete records
            {
                std::cout << "Enter a name: ";                  // Only find by name
                auto pr = find_elements(by_name);
                auto count = std::distance(pr.first, pr.second);  // Number of elements
                if(count == 1)
                {                                               // If there's just the one...
                    by_number.erase(pr.first->second);            // ...delete from numbers container
                    by_name.erase(pr.first);                      // ...delete from names container
                }
                else if(count > 1)
                { // There's more than one
                    std::cout << "There are " << count << " records for "<< pr.first->first << ". Delete all(Y or N)? ";
                    std::cin >> choice;
                    if(std::toupper(choice) == 'Y')
                    { // Erase records from by_number container first
                        for(auto iter = pr.first; iter != pr.second; ++iter)
                        {
                            by_number.erase(iter->second);
                        }
                        by_name.erase(pr.first, pr.second);         // Now delete from by_name
                    }
                }
            }
            break;

            case 'F':                                         // Find a record
                std::cout << "Find by name(Y or N)? ";
                std::cin >> choice;
                if(std::toupper(choice) == 'Y')
                {
                    std::cout << "Enter first name and second name: ";
                    list_range(find_elements(by_name));
                }
                else
                {
                    std::cout << "Enter area code, exchange, and number separated by spaces: ";
                    list_range(find_elements(by_number));
                }
                break;

            case 'L':                                         //List all records
                std::cout << "List by name(Y or N)? ";
                std::cin >> choice;
                if(std::toupper(choice) == 'Y')
                    list_elements(by_name);
                else
                    list_elements(by_number);
                break;
            case 'Q':
                break;

            default:
                std::cout << "Invalid command - try again.\n";
        }
    }
}
```

这段代码很好理解。在输入选项后，程序就会执行相应的操作，直到输入 'q' 或 'Q'。 循环体是一条大的 switch 语句，用来选择合适的操作。

添加元素只涉及每个容器元素的生成，by_name 使用的键/对象值和 by_number 使用的是相反的。

可以用 find_elements() 来删除 by_name 容器的元素，为了保证两个容器中内容的同步，需要删除 by_number 容器的对应元素。为了能够从 by_name 容器移除多个元素，需要用定义元素范围的迭代器作为 erase() 的参数。如果所有元素的键相同，就可以以这个范围内的第一个元素的键为参数来删除它们，例如：

```
by_name.erase(pr.first->first); // Delete elements with the specified key
```

为了查找操作，find_elements() 模板实例返回的 pair 必须直接传给 list_range() 模板的实例。编译器会自动保证生成合适的调用。最后，为了可以列出元素，必须用指定的键为参数来调用一个 list elements() 模板的实例，从而输出元素。

由于程序运行结果很占篇幅，各位读者可自行运行程序查看运行结果。