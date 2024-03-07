# C++ map 插入数据（STL map 插入数据）详解

map<K，T> 容器的成员函数 insert() 有多个版本，它们可以在 map 中插入一个或多个 pair<const K,T> 对象。只能插入 map 中不存在的元素。下面这个代码片段展示了如何插入单个元素：

```
std::map<std:: string,size_t> people {std::make_pair ("Ann",25),std::make_pair("Bill",46) , std::make_pair ("Jack",32), std::make_pair("Jill",32)};
auto pr = std::make_pair("Fred",22); //Create a pair element and insert it
auto ret_pr = people.insert(pr);
std::cout << ret_pr.first->first << " "<< ret_pr.first->second<< "" << std:: boolalpha <<ret_pr.second << "\n"; // Fred 22 true
```

第一条语句生成了一个 map 容器，并用初始化列表中的 4 个值对它进行了初始化；在这种情况下，这些值会被隐式转换为要求的类型。第二条语句生成了另一个被插入的 pair 对象 pr。pr 对象的类型是 pair<const char*,int>，因为 make_pair<>() 函数模板的类型参数是从参数类型推断出来的；但是在 insert() 操作中，这个对象会被隐式转换为容器元素类型。当然，如果不想依靠隐式转换，可以生成所要求类型的 pair 对象：

```
auto pr = std::make_pair<std:: string, size_t> (std:: string { "Fred"},22);
```

make_pair<>() 模板的显式类型参数决定了返回的 pair 对象的类型。可以把文字字符串作为第一个参数，然后通过隐式转换创建键需要的字符串对象。可以省略 make_pair<>() 的模板类型参数，让编译器去推断它们。假设像下面这样声明：

```
auto pr = std::make_pair ("Fred",22) ; // pair<const char*, int>
```

这里会返回和所要求类型不同的 pair 对象。当允许编译器推断模板参数类型时，make_pair() 的参数可以准确地确定模板参数的类型。第一个参数是一个 const char* 类型的字符串，第二个参数是 int 类型。尽管已经说明了元素的类型，但在这种情况下，并没有多大的用处，因为在插入一个新元素时，pair 对象可以被隐式转换为容器所需类型。当 make_pair() 的参数的类型不能隐式转换成容器的键和对象的类型时，就需要注意了。

成员函数 insert() 会返回一个 pair<iterator,bool> 对象。对象的成员 first 是一个迭代器，它要么指向插入元素，要么指向阻止插入的元素。如果 map 中已经保存了一个和这个键相同的对象，就会出现后面这种情况。这个对象的成员变量 second (布尔型)是返回对象，如果插入成功，返回值为 true，否则为 false。

输出语句像我们看到的那样，访问插入 pair 的成员变量 first 的表达式是 ret_pr.first->first。ret_pr 的成员变量 first 是一个指向 pair 对象的迭代器，所以可以使用->操作符来访问它的成员变量 first。输出展示了插入的元素。可以通过下面这个循环进行验证：

```
for (const auto& p : people)
    std::cout << std::setw(10) << std::left << p.first << " "<< p.second <<"\n";
```

循环变量 p 通过引用依次访问 map 容器 people 中的每个元素。输出如下：

Ann 25
Bill 46
Fred 22
Jack 32
Jill 32

元素是以键的升序排列的，因为 map 中默认使用 less<string> 函数对象对它们进行排序。

通过执行下面这两条语句，可以看出元素插入后的效果：

```
ret_pr = people.insert(std::make_pair("Bill", 48));
std:: cout << ret_pr.first->first <<" "<<ret_pr.first->second<< " "<<std::boolalpha<<ret_pr.second << "\n"; // Bill 46 false
```

程序会输出如注释所示的内容。insert() 返回了一个 pair 对象 ret_pr，它的成员变量 first 指向 map 中已有的和键匹配的元素，成员变量 second 为 false，表明元素没有插入成功。

当元素已经存在时，如果想将键值“Bill”对应的年龄值改为 48，可以像下面这样使用 insert() 返回的 pair 对象来做到这一点：

```
if(!ret_pr.second) // If the element is there change the age
    ret_pr.first—>second = 48;
```

当键已经存在于 map 容器中时，ret_pr 的成员变量 second 为 false，所以这段代码会将 map 中这个元素的成员变量 second 的值设为 48。

可以用 pair 构造函数生成的对象作为 insert() 的参数：

```
ret_pr = people.insert(std::pair<const std::string, size_t> {"Bill", 48});
```

这里会调用一个具有右值引用参数的 insert() 版本，所以假如元素不在容器中，那么它会被移到容器中。

也可以提供一个提示符来指出元素插入的位置。提示符是迭代器的形式，它指向容器中的一个现有元素，通常从提示符指示的位置开始查找新元素的插入位置。好的提示符可以提高插入操作的速度，反之亦然。例如：

```
auto ret_pr = people.insert(std::make_pair("Jim", 48));
people.insert (ret_pr.first, std::make_pair ("Ian", 38)）；
```

第一条语句插入了一个元素，并像前面那样返回了一个对象。pair 对象的成员变量 first 是一个指向被插入的元素或容器中与插入元素有相同键的元素的迭代器。

下一个 insert() 函数的第一个参数和上面的提示符有关，所以这里就是插入元素的地方。insert() 的第二个参数指定的新元素会被插入到提示符的前面，并尽可能地靠近它。如果提示符不能以这种方式使用，那么将忽略它。同样地，如果被插入的元素已经在 map 中，会导致元素插入失败。带提示符的 insert() 调用会返回一个指向被插入元素或容器中阻止此插入操作的元素的迭代器，因此可以使用返回值来确定插入是否成功。当确定元素不存在时，可以只提供一个插入符，这是一个好的想法。如果不那么确定，而且仍然想使用插入符，map 中的 count() 函数对我们有一些帮助。它会返回 map 中指定键对应元素的数目，这个数目可能是 0 或 1。

因此代码可以这样写：

```
if (!people.count("Ian"))
    people.insert (ret_pr.first, std::make_pair ("Ian", 38));
```

只有当 count() 函数返回 0 时，insert() 才会被调用，这说明“Ian”键不在 map 中。当然，在不用提示插入元素时，需要做一次这样的检查，但 insert() 的返回值不管怎样都能告诉我们插入结果。

也可以将外部源中的一段元素插入 map 中，这些元素不必来自另一个 map 容器，但必须和被插入容器中的元素是同类型。这里有一些示例：

```
std::map<std::string, size_t> crowd {{"May", 55}, {"Pat",66}, {"Al", 22}, {"Ben", 44}};
auto iter = std::begin(people);
std::advance(iter, 4);  // begin iterator+ 4
crowd.insert(++std::begin(people),iter); // Insert 2nd, 3rd, and 4th elements from people
```

这里生成了一个新的 map 容器 crowd，它有 4 个初始元素。iter 被初始化为 people 的开始迭代器。map 容器的迭代器是双向的，这样就可以对它们进行自增或自减，但是不能加上或减去一个值。这里使用了一个在前面章节中用过的 advance() 函数模板的实例来对 iter 加 4，所以它将会指向第 5 个元素，它在下一行被当作 crowd 的成员函数 insert() 的参数，用来作为指定元素段的结束迭代器。map 容器 people 的开始迭代器加 1，然后用它作为插入元素段的开始迭代器，所以会从 crowd 的第 2 个元素开始插入 3 个元素。

下面是一个接受初始化列表作为参数的 insert() 版本：

```
crowd.insert({{"Bert", 44}, {"Ellen”, 99}});
```

这里会将初始化列表中的两个元素插入到 map 容器 crowd 中。参数表达式生成的 initializer_list<> 对象是 initializer_list<const string，size_t> 类型，因为编译器知道这时 insert() 函数的参数是这种类型。当然，也可以单独创建一个初始化列表，然后将它作为参数传递给 insert() 函数：

```
std::initializer_list<std::pair<const std:: string, size_t>>init {{"Bert", 44}, {"Ellen", 99}};
crowd.insert(init);
```

initializer-list 模板的第一个类型参数必须为 const 类型。initializer_list <string,size_t> 无法隐式转换为 initializer_list<const string, size_t>，所以前者类型的对象不能作为 insert() 函数的参数。

下面你会看到这些操作的一个完整示例。此处会定义一个有点不同的对象。Name 类型代表人名，这个类定义的头文件的内容如下：

```
// Defines a person's name
#ifndef NAME_H
#define NAME_H
#include <string>                                // For string class
#include <ostream>                               // For output streams
#include <istream>                               // For input streams

class Name
{
private:
    std::string first {};
    std::string second {};

public:
    Name(const std::string& name1, const std::string& name2) : first (name1), second (name2) {}
    Name() = default;
    // Less-than operator
    bool operator<(const Name& name) const
    {
        return second < name.second || (second == name.second && first < name.first);
    }
    friend std::istream& operator>>(std::istream& in, Name& name);
    friend std::ostream& operator<<(std::ostream& out, const Name& name);
};

// Extraction operator overload
inline std::istream& operator>>(std::istream& in, Name& name)
{
    in >> name.first >> name.second;
    return in;
}

// Insertion operator overload
inline std::ostream& operator<<(std::ostream& out, const Name& name)
{
    out << name.first + " " + name.second;
    return out;
}

#endif
```

这个类非常简单，只有两个 string 类型的私有成员变量 first 和 second。这个构造函数可以接受 string 类型的参数或字符串常量参数。为了可以用这种对象作为 map 容器的键，必须为这个类定义 operator<()。为了便于对象的输入输出，也需要为流定义插入和提取操作。

map 容器中的元素是 std::pair<const Name，size_t> 类型，我们可以用下面定义的别名来简化代码：

```
using Entry = std::pair<const Name, size_t>;
```

当容器是 map<name;site_t> 类型时，我们只能用 Entry 作为容器元素的类型。为了便于 map 元素的输出，我们可以把别名放到一个函数的定义中：

```
Entry get_entry()
{
    std::cout << "Enter first and second names followed by the age: ";
    Name name {};
    size_t age {};
    std::cin >> name >> age;
    return make_pair(name, age);
}
```

从 cin 先后读入了一个 Name 对象和一个年龄值，并用它们生成了一个 pair 对象。从输入中读取 name 激发了定义在 Name.h 中的 istream 对象的重载函数 operator>>()，同样也支持流对象对 Name 对象的读入。

可以输出容器元素的辅助函数也是很有用的：

```
void list_entries(const map<Name, size_t>& people)
{
    for(auto& entry : people)
    {
        std::cout << std::left << std::setw(30) << entry.first<< std::right << std::setw(4) << entry.second << std::endl;
    }
}
```

这里只用了基于范围的 for 循环来对元素进行遍历。循环变量 entry 依次引用 map 的每个元素。每一个 map 元素都是一个 pair 对象，它的成员变量 first 是 Name 类型的对象，成员变量 second 是 size_t 类型的值。

包含 main() 函数的源文件中的内容如下：

```
// Storing names and ages
#include <iostream>                              // For standard streams
#include <iomanip>                               // For stream manipulators
#include <string>                                // For string class
#include <map>                                   // For map container class
#include <utility>                               // For pair<> & make_pair<>()
#include <cctype>                                // For toupper()
#include "Name.h"
using std::string;
using Entry = std::pair<const Name, size_t>;
using std::make_pair;
using std::map;

// Create a map entry from input
Entry get_entry()
{
    std::cout << "Enter first and second names followed by the age: ";
    Name name {};
    size_t age {};
    std::cin >> name >> age;
    return make_pair(name, age);
}

// Output the elements in a map
void list_entries(const map<Name, size_t>& people)
{
    for(auto& entry : people)
    {
        std::cout << std::left << std::setw(30) << entry.first
              << std::right << std::setw(4) << entry.second << std::endl;
    }
}

int main()
{
    map<Name, size_t> people {{{"Ann", "Dante"}, 25}, {{"Bill", "Hook"}, 46}, {{"Jim", "Jams"}, 32}, {{"Mark", "Time"}, 32}};

    std::cout << "\nThe initial contents of the map is:\n";
    list_entries(people);

    char answer {'Y'};
    std::cout << "\nEnter a Name and age entry.\n";
    while(std::toupper(answer) == 'Y')
    {
        Entry entry {get_entry()};
        auto pr = people.insert(entry);
        if(!pr.second)
        { // It's there already - check whether we should update
            std::cout << "Key \"" << pr.first->first << "\" already present. Do you want to update the age (Y or N)? ";
            std::cin >> answer;
            if(std::toupper(answer) == 'Y')
                pr.first->second = entry.second;
        }
        // Check whether there are more to be entered
        std::cout << "Do you want to enter another entry(Y or N)? ";
        std::cin >> answer;
    }

    std::cout << "\nThe map now contains the following entries:\n";
    list_entries(people);
}
```

定义一些额外的别名可以减少代码冗余。可以用 using::namespace 来完全消除对 std 名称限定的需要，但不赞成这么做，因为 std 中的所有命名都被有效导入到当前作用域内，这违背了定义命名空间的初衷。

map 容器的定义中有一些初始值，它们是初始化列表中的元素。这里只是为了说明如何在这种情况下使用嵌套花括号。在定义元素的每个初始化列表中，花括号中的 Name 对象都是初始化列表，每个初始化元素的花括号中都是一个 Name 对象和一个年龄值。最外面的一对花括号包括了所有元素的初始值。

辅助函数 list_entries() 用来显示容器的初始状态。从 for 循环中读入了更多的 entry 对象。循环由 answer 的值控制，如果一开始它的值就是 'Y'，那么最少执行一次循环，最少从键盘输入一个元素。entry 对象的类型是 Entry 它也是容器元素的类型。辅助函数 get_entry() 的返回对象被作为 entry 的初始值。然后将它作为 insert() 的参数插入到容器中。这个函数返回的 pair 对象有一个成员变量 first,它指向容器中和 entry 的键匹配的元素。如果在插入之前，容器中就存在这个元素，那么它指向的是原始容器中的元素。如果键已经存在于容器中，插入不会成功，并且 pr 的成员变量 second 为 false。

pr.first 是容器中元素的一个迭代器，因此 pr.first->second 可以访问与键关联的对象，如果用户确认需要更新，pr.first->second 的值会变为 entry.second。循环中的最后一个操作可以决定是否输入更多的 entry。在不需要输入更多的 entry 时，循环结束，用 list_entries() 函数输出这个容器最终的内容。

下面是这个示例的一些输出结果:

The initial contents of the map is:
Ann Dante                       25
Bill Hook                       46
Jim Jams                        32
Mark Time                       32

Enter a Name and age entry.
Enter first and second names followed by the age: Emma Nate 42
Do you want to enter another entry(Y or N)? y
Enter first and second names followed by the age: Emma Nate 43
Key "Emma Nate" already present. Do you want to update the age (Y or N)? Y
Do you want to enter another entry(Y or N)? y
Enter first and second names followed by the age: Eamonn Target 56
Do you want to enter another entry(Y or N)? n

The map now contains the following entries:
Ann Dante                       25
Bill Hook                       46
Jim Jams                        32
Emma Nate                       43
Eamonn Target                   56
Mark Time                       32

这些元素是以键的升序排列的，因为容器中的元素是使用 less<Name> 进行排序的。Name::operator<() 先比较姓，在姓相同时才比较名。这导致返回的姓名是正常的顺序。