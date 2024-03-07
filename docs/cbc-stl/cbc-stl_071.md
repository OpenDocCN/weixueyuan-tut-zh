# C++ multiset 用法详解（附带完整示例）

multiset<T> 容器就像 set<T> 容器，但它可以保存重复的元素。这意味我们总可以插入元素，当然必须是可接受的元素类型。默认用 less<T> 来比较元素，但也可以指定不同的比较函数。在元素等价时，它必须返回 false。例如：

```
std::multiset<string, std::greater<string>> words{{"dog", "cat", "mouse"}, std::greater<string>()};
```

这条语句定义了一个以 string 为元素的 multiset，它以 greater<string> 为构造函数的第二个参数。构造函数的第一个参数是一个初始化列表，它为这个容器指定了三个初始元素。和 set 一样，如果它的两个元素相等，那么它们就是匹配的。在一个有比较运算符 comp 的 multiset 中，如果表达式 !(a comp b) && !(b comp a) 为 true，那么元素 a 和 b 就是相等的。

*   multiset 容器和 set 容器有相同的成员函数，但是因为 multiset 可以保存重复元素，有些函数的表现会有些不同。和 set 容器中的成员函数表现不同的是：
*   insert() 总是可以成功执行。当插入单个元素时，返回的迭代器指向插入的元素。当插入一段元素时，返回的迭代器指向插入的最后一个元素。
*   emplace() 和 emplace_hint() 总是成功。它们都指向创建的新元素。
*   find() 会返回和参数匹配的第一个元素的迭代器，如果都不匹配，则返回容器的结束迭代器。
*   equal_range() 返回一个包含迭代器的 pair 对象，它定义了一个和参数匹配的元素段。如果没有元素匹配的话，pair 的第一个成员是容器的结束迭代器；在这种情况下，第二个成员是比参数大的第一个元素，如果都没有的话，它也是容器的结束迭代器。
*   lower_bound() 返回和参数匹配的第一个元素的迭代器，如果没有匹配的元素，会返回容器的结束迭代器。返回的迭代器和 range() 返回的 pair 的第一个成员相同。
*   upper_bound() 返回的迭代器和 equal_range() 返回的 pair 的第二个成员相同。
*   count() 返回和参数匹配的元素的个数。

用 multiset 容器代替 map，实现分析单词出现次数的程序：

```
// Determining word frequency
#include <iostream>                               // For standard streams
#include <iomanip>                                // For stream manipulators
#include <string>                                 // For string class
#include <sstream>                                // For istringstream
#include <algorithm>                              // For replace_if() & for_each()
#include <set>                                    // For map container
#include <iterator>                               // For advance()
#include <cctype>                                 // For isalpha()

using std::string;

int main()
{
    std::cout << "Enter some text and enter * to end:\n";
    string text_in {};
    std::getline(std::cin, text_in, '*');

    // Replace non-alphabetic characters by a space
    std::replace_if(std::begin(text_in), std::end(text_in), [](const char& ch){ return !isalpha(ch); }, ' ');

    std::istringstream text(text_in);             // Text input string as a stream
    std::istream_iterator<string> begin(text);    // Stream iterator
    std::istream_iterator<string> end;            // End stream iterator

    std::multiset<string> words;                  // Container to store words
    size_t max_len {};                            // Maximum word length

    // Get the words, store in the container, and find maximum length
    std::for_each(begin, end, &max_len, &words{
                words.emplace(word);
                max_len = std::max(max_len, word.length());
    });

    size_t per_line {4},                           // Outputs per line
         count {};                               // No. of words output

    for(auto iter = std::begin(words); iter != std::end(words); iter = words.upper_bound(*iter))
    {
        std::cout << std::left << std::setw(max_len + 1) << *iter<< std::setw(3) << std::right << words.count(*iter) << "  ";
        if(++count % per_line == 0)
            std::cout << std::endl;
    }
    std::cout << std::endl;
}
```

在输入过程中移除了输入中的非字母字符。单词是由 foreach 从 istringstream 对象的文本中提取的，然后把它们传给了一个 lambda 表达式，这个表达式是 for_each() 的最后一个参数，用来创建 multiset 容器中的元素。

从 text 中获取的每个单词都被单独保存，因为一般来说，容器中会出现重复的元素。for 循环遍历 multiset 容器 words 中的迭代器，从指向第一个元素的开始迭代器开始。容器中的元素是有序的，因而相等的元素位置是连续的。通过调用容器的成员函数 count()，可以获取和它的参数 iter 所指向元素相等的元素的个数。

每次循环迭代结束后，iter 被设为 upper_bound() 返回的值，它指向一个不同于当前元素的元素。如果不存在这样的元素，upper_bound() 会返回容器的结束迭代器，循环就此结束。

因为 multiset 中的元素是有序的，所以可以在循环中用相等的单词数量来增加迭代器，例如：

```
size_t word_count {};   // Number of identical words
for(auto iter = std::begin(words); iter != std::end(words);)
{
    word_count = words.count(*iter);
    std::cout << std::left << std::setw(max_len + 1) << *iter << std::setw(3) << std::right << word_count << " ";
    if(++count % per_line == 0)
        std::cout << std::endl;
    std::advance(iter, word_count);
```

这种方式比之前的循环更好。但这个版本的循环结束不是那么明显。下面是示例输出：

Enter some text and enter * to end:
He was saying godnight to his horse.
He was saying goodnight to his horse,
And as he was saying goodnight to his horse,he was saying goodnight to his horse.
"Goodnight horse,goodnight horse",he was saying goodnight to his horse.*
And         1  Goodnight   1  He          2  as          1
godnight    1  goodnight   5  he          3  his         5
horse       7  saying      5  to          5  was         5

## 保存派生类对象的指针

我们可能想在 set 或 multiset 容器中保存派生类对象的指针，可以通过将元素类型指定为基类对象类型的指针来做到这一点。这里主要担心的是比较函数，它必须可以比较指向不同类型的派生类对象的基类指针。通常我们会自己定义函数来做这件事，这没有任何难度。但如何去比较，取决于我们是否对元素顺序有任何要求。如果不在乎元素的排序方式，可以使用 owner_less<T> 实例。但需要记住，检索元素要用指向相同对象的指针，而不是使用相等的对象。让我们思考一个示例。使用一个 multiset 容器，即使我们没有重复元素要保存，但会有一些不同类型的元素。

假设我们想在容器中保存每个人所拥有的宠物，这里宠物的类型是由一个派生于基类 Pet 的类定义的。这个类被定义在头文件 Pet_Classes.h 中，代码如下：

```
using std::string;

class Pet
{
protected:
    string name {};

public:
    virtual ~Pet(){}                               // Virtual destructor for base class
    const string& get_name() const { return name;  }

    virtual bool operator<(const Pet& pet) const
    {
        auto result = std::strcmp(typeid(*this).name(), typeid(pet).name());
        return (result < 0) || ((result == 0) && (name < pet.name));
    }
    friend std::ostream& operator<<(std::ostream& out, const Pet& pet);
};
```

需要注意 Pet 类的成员函数 operator<() 的定义。为了获得派生类的多态行为，它被指定为虚函数。这里使用了运算符 typeid，它会创建一个 type_info 对象，这个对象封装了操作数的类型。

使用 typeid 需要包含 typeinfo 头文件。调用 type_info 对象的成员函数 name() 会返回一个 C 风格的字符串，这是类型名实现定义的一种表示。在此系统上，类型名加上了前缀 "class"，因此对于 My_Type 类型的对象，name() 返回的是 "class My_Type"，在你的系统上可能会有所不同。

用定义在 cstring 头文件中的 strcmp() 来比较类型名字符串。如果第一个参数小于第二个参数，这个函数会返回一个负数；如果两个参数相等，返回 0，否则返回一个正数。operator<() 函数会返回两个表达式或运算后的结果。如果第一个表达式为 true，这个函数总是返回 true。当前对象的类型名小于参数对象的类型名时，会出现这种情况，因而对象主要依靠类型来排序。当第一个表达式是 false 时，这个表达式的结果取决于第二个表达式的结果。当类型名字符串相等，且比较运算的左操作数 name 小于右操作数的成员 name 时，第二个表达式才为 true。

返回表达式中对相同类型名的比较非常重要。set 或 map 容器指定的比较运算必须是严格弱序的。在其他的条件中，这要求如果 a<b 为 true，那么 b<a 必须为 false。不比较两个类型名是否相等。这个返回值的表达式无法满足这个条件。

当对保存派生类对象的容器进行排序时，这就可能会导致程序崩溃。你很容易明白这是如何产生的。假设将一个 name 为"Tiddles”的 Cat 对象 cat 和一个 name 为"Rover"的 Dog 对象 dog 比较，因为类型名，表达式 cat < dog 为 true；而表达式 dog < cat 也为 true，因为宠物名。这两个对象同时小于彼此可能会产生一些问题。

当然可以不使用 strcmp()，可以将 type_info 的成员函数 name() 返回的 null 结尾的字符串转换为 string 文件类型，然后就可以用 < 运算符比较它们。

Pet_Classes.h 头文件中为输出流定义的插入运算如下：

```
inline std::ostream& operator<<(std::ostream& out, const Pet& pet)
{
    return out << "A " <<string {typeid(pet).name()}.erase(0,6) << " called " << pet.name;
}
```

这段代码输出了类型名和宠物名。类型名字符串的表达式首先将 C 风格的字符串转换为 string 类型，然后从 string 移除前 6 个字符 "class”。如果你的系统使用的是不同的类型名，需要对代码做一些修改。

为简单起见，定义三个派生于 Pet 的类：Cat、Dog 和 Mouse。除了类型不同外，它们的定义在本质上是相同的。Dog 类的定义如下：

```
class Dog : public Pet
{
public:
    Dog() = default;
    Dog(const string& dog_name)
    {
        name = dog_name;
    }
};
```

在这个构造函数中，初始化了继承的成员变量。所有派生类的定义都在 Pet 头文件中。

## 定义容器

用 multiset 容器保存 shared_ptr<Pet> 对象，可以用两个 using 指令来为它们指定类型别名：

```
using Pet_ptr = std::shared_ptr<Pet>; // A smart pointer to a pet
using Pets = std::multiset<Pet_ptr>; //A set of smart pointers to pets
```

Pet_ptr 别名简化了 multiset 容器的定义，Pets 别名简化了 map 容器的定义。这里会用 map 容器来保存 multiset 容器，并以人名作为 map 的键。Pets 容器可以保存 Pet 对象的指针，也能保存 Cat、Dog、Mouse 对象的指针。

需要为 Pet_ptr 对象的 multiset 容器定义一个小于运算符：

```
inline bool operator< (const Pet_ptr& p1, const Pet_ptr& p2)
{
    return *p1 < *p2;
}
```

这里解引用传入的指针参数，然后将解引用得到的对象传入 Pet 派生类的虚函数 operator<()。在这个示例中，multiset 容器默认的函数对象 less<Pet_ptr> 会调用上面这个函数。

这里有两个更有用的 using 用法：

```
using std::string;
using Name = string;
```

Name 的别名可以使 map 容器的键类型更加清楚明白。在 main() 中按如下方式定义 map:

```
std::map<Name, Pets> peoples_pets;
```

容器中的元素是 pair<Name, Pets> 对象，它的完整形式是 pair<string, multiset<shared_ptr <Pet>>，后面这种表示形式不是那么直观。

## 定义示例的 main()函数

用辅助函数从标准输入流读取人名和他们的宠物:

```
// Read in all the pets for a person
Pets get_pets(const Name& person)
{
    Pets pets;
    std::cout << "Enter " << person << "'s pets:\n";
    char ch {};
    Name name {};
    while(true)
    {
        std::cin >> ch;
        if(toupper(ch) == 'Q') break;
        std::cin >> name;
        switch(std::toupper(ch))
        {
            case 'C':
                pets.insert(std::make_shared<Cat>(name));
                break;
            case 'D':
                pets.insert(std::make_shared<Dog>(name));
                break;
            case 'M':
                pets.insert(std::make_shared<Mouse>(name));
                break;
            default:
                std::cout << "Invalid pet ID - try again.\n";
        }
    }
    return pets;
}
```

代码虽然看起来很多，但都很简单。首先会创建一个 Pets 类型的局部 multiset 容器。将人名作为参数传入函数，作为输入提示，然后在一个死循环中读入他的宠物。用首字母来定义宠物的类型——'C' 是猫，'D' 是狗，以此类推。在 main() 中会为这种表示方式创建一个提示。类型字符后面是宠物的名字，输入 'Q' 会结束当前输入。swich 语句会创建适当类型的 shared_ptr<T> 对象，然后把它保存到 pets 容器中。当输入结束时，会以移动运算的方式返回局部对象 pets。

程序会输出 Pets 容器中的宠物，因此这里需要实现一个流插入运算符：

```
// Stream insertion operator for pointers to pets
inline std::ostream& operator<<(std::ostream& out, const Pet_ptr& pet_ptr)
{
  return out << " " << *pet_ptr;
}
```

解引用智能指针，然后用插入运算符将对象写入流 out。因此这里会调用 Pet 类的友元函数 operator<<()。为了输出 map 容器中的元素，在另一个函数的定义中使用它:

```
// REad all the pets for a given person
void list_pets(const std::pair<Name, Pets>& pr)
{
    std::cout << "\n" << pr.first << ":\n";
    std::copy(std::begin(pr.second), std::end(pr.second), std::ostream_iterator<Pet_ptr>(std::cout, "\n"));
}
```

元素是 pair 对象，它的第一个成员是人名，第二个成员是一个 multiset 容器，里面包含了指向宠物的指针。在将 pair 的第一个成员输出到标准输出流后，用 copy() 算法输出第二个成员的元素。copy() 的前两个参数都是迭代器，它们定义了拷贝元素的范围。第三个参数指定了拷贝操作的目的地，是一个 ostream_iterator<Pet_ptr> 对象。然后调用 operator<<() 函数，将 Pet_ptr 作为它的第二个参数，这个函数然后会调用 Pet 类的友元函数 operator<<0。

main() 函数的完整代码为：

```
// Storing pointers to derived class objects in a multiset container
#include <iostream>                              // For standard streams
#include <string>                                // For string class
#include <algorithm>                             // For copy() algorithm
#include <iterator>                              // For ostream_iterator
#include <map>                                   // For map container
#include <set>                                   // For multiset container
#include <memory>                                // For smart pointers
#include <cctype>                                // For toupper()
#include "Pet_Classes.h"

using std::string;
using Name = string;
using Pet_ptr = std::shared_ptr<Pet>;            // A smart pointer to a pet
using Pets = std::multiset <Pet_ptr>;            // A set of smart pointers to pets

// Compare shared pointers to pets
inline bool operator<(const Pet_ptr& p1, const Pet_ptr& p2)
{
  return *p1 < *p2;
}

// Stream insertion operator for pointers to pets
inline std::ostream& operator<<(std::ostream& out, const Pet_ptr& pet_ptr)
{
  return out << " " << *pet_ptr;
}

// Read in all the pets for a person
Pets get_pets(const Name& person)
{
    Pets pets;
    std::cout << "Enter " << person << "'s pets:\n";
    char ch {};
    Name name {};
    while(true)
    {
        std::cin >> ch;
        if(toupper(ch) == 'Q') break;
        std::cin >> name;
        switch(std::toupper(ch))
        {
            case 'C':
                pets.insert(std::make_shared<Cat>(name));
                break;
            case 'D':
                pets.insert(std::make_shared<Dog>(name));
                break;
            case 'M':
                pets.insert(std::make_shared<Mouse>(name));
                break;
            default:
                std::cout << "Invalid pet ID - try again.\n";
        }
    }
    return pets;
}

// REad all the pets for a given person
void list_pets(const std::pair<Name, Pets>& pr)
{
    std::cout << "\n" << pr.first << ":\n";
    std::copy(std::begin(pr.second), std::end(pr.second), std::ostream_iterator<Pet_ptr>(std::cout, "\n"));
}

int main()
{
    std::map<Name, Pets> peoples_pets;             // The people and their pets
    char answer {'Y'};
    string name {};
    std::cout << "You'll enter a person's name followed by their pets.\n"<< "Pets can be identified by C for cat, D for dog, or M for mouse.\n"<< "Enter the character to identify each pet type followed by the pet's name.\n"<< "Enter Q to end pet input for a person.\n";
    while(std::toupper(answer) == 'Y')
    {
        std::cout << "Enter a name: ";
        std::cin >> name;
        peoples_pets.emplace(name, get_pets(name));
        std::cout << "Another person(Y or N)? ";
        std::cin >> answer;
    }

    // Output the pets for everyone
    std::cout << "\nThe people and their pets are:\n";
    for(const auto& pr : peoples_pets)
        list_pets(pr);
}
```

在 main() 中定义了人名和宠物的 map 容器后，会有一个提示来说明输入过程。while 循环控制所有输入。通过调用 people_pets 容器的成员函数 emplace() 来向它添加元素，这个函数会在合适的位置创建元素。name 是它的第一个参数，第二个参数是 get_pets() 返回的 multiset 容器。当输入结束时，通过迭代访问 map 元素的方式，输出人名及其宠物。用  map 容器的当前 pair 元素作为辅助函数 list_pets() 的参数，输出每个人的相关信息。

由于运行结果篇幅过长，文章中不再列举，读者可自行运行此完整代码查看运行结果。通过运行代码，宠物是以宠物类型名的字母升序输出的，这和我们期望的一样。输出表明我们成功在容器中保存了指向派生类的基类类型的智能指针。