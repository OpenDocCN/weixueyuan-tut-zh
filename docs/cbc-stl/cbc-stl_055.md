# C++ multimap(STL multimap)的使用详解

multimap 容器保存的是有序的键/值对，但它可以保存重复的元素。multimap 中会出现具有相同键的元素序列，它们会被添加到容器中。multimap 和 map 有相同范围的构造函数，默认的比较键的函数是 less<K>()。

multimap 大部分成员函数的使用方式和 map 相同。因为重复键的原因，multimap 有一些函数的使用方式和 map 有一些区别。接下来介绍 multimap 和 map 容器不同的那些成员函数的用法。

multimap 容器的成员函数 insert() 可以插入一个或多个元素，而且插入总是成功。这个函数有很多的版本都可以插入单个元素，它们都会返回一个指向插入元素的迭代器。下面有一个示例，假设我们已经使用了声明 using std::string：

```
std::multimap<string, string〉 pets; // Element is pair{pet_type, pet_name}
auto iter = pets.insert (std::pair<string, string>{string{"dog"}, string{"Fang"}});
iter = pets.insert(iter, std::make_pair("dog", "Spot")); // Insert Spot before Fang
pets.insert(std::make_pair("dog", "Rover"));// Inserts Rover after Fang
pets.insert (std::make_pair ("cat", "Korky"));// Inserts Korky before all dogs
pets.insert ({{ "rat", "Roland"}, {"pig", "Pinky" }, {"pig", "Perky"}});//Inserts list elements
```

第三条语句的第一个参数是一个作为提示符的迭代器，它说明了元素应该被插入的位置。元素会被立即插入到 iter 所指向元素的前面，因此，这使我们可以覆盖默认的插入位置。对于默认的插入位置来说，元素会被插入到先前插入的键为 "dog" 的元素的后面。元素默认是按照键的升序插入的。如果没有用提示符改变插入位置，有相同键的元素的位置和插入位置相同。最后一条语句插入了一些初始化列表中的元素。有高级版本的 insert()，它可以接收两个迭代器参数，用来指定插入元素的范围。

和 map —样，multimap 的成员函数 emplace() 可以在容器的适当位置构造元素。在插入具有相同键的元素时，可以使用 multimap 的成员函数 emplace_hint()，可以通过为这个函数提供一个迭代器形式的提示符来控制元素的生成位置：

```
auto iter = pets.emplace("rabbit”,"Flopsy");
iter = pets.emplace_hint (iter, "rabbit", "Mopsy");// Create preceding Flopsy
```

这两个函数都返回一个指向插入元素的迭代器。emplace_hint() 函数尽可能近地在第一个参数所指向位置的前面生成一个新元素。如果只使用 emplace() 来插入 "Mopsy"，它可能会被插入到当前所有键为 "rabbit" 的元素的后面。

multimap 不支持下标运算符，因为键并不能确定一个唯一元素。和 map 相似，multimap 也不能使用 at() 函数。multimap 的成员函数 fmd() 可以返回一个键和参数匹配的元素的迭代器。例如：

```
std::multimap<std::string, size_t> people {{"Ann",25},{"Bill", 46}, {"Jack", 77}, {"Jack", 32},{"Jill", 32}, {"Ann", 35} };
std::string name {"Bill"};
auto iter = people.find(name);
if (iter ! = std::end (people))
    std::cout << name << " is " << iter->second << std::endl;
iter = people.find ("Ann");
if (iter != std::end(people))
    std::cout << iter->first << " is " << iter->second <<std::endl;
```

如果没有找到键，会返回一个结束迭代器，所以我们应该总是对返回值进行检查。第一个 find() 调用的参数是一个键对象，因为这个键是存在的，所以输出语句可以执行。第二个 find() 调用的参数是一个字符串常量，它说明参数不需要和键是相同的类型。对容器来说，可以用任何值或对象作为参数，只要可以用函数对象将它们和键进行比较。最后一条输出语句也可以执行，因为有等于 "Ann" 的键。事实上，这里有两个等于 "Ann" 的键，你可能也会得到不同的运行结果。

如果使用 multimap 容器，几乎可以肯定它会包含键重复的元素；否则，就应该使用 map。一般来说，我们想访问给定键对应的所有元素。成员函数 equal_range() 就可以做到这一点。它会返回一个封装了两个迭代器的 pair 对象，这两个迭代器所确定范围内的元素的键和参数值相等。例如：

```
auto pr = people.equal_range("Ann");
if(pr.first != std::end(people))
{
    for (auto iter = pr.first ; iter != pr.second; ++iter)
        std:cout << iter->first << " is " << iter->second << std::endl;
}
```

equal_range() 的参数可以是和键同类型的对象，或是不同类型的但可以和键比较的对象。返回的 pair 对象的成员变量 first 是一个迭代器，它指向第一个大于等于参数的元素；如果键和参数相等的元素存在的话，它是第一个键和参数相同的元素。如果键不存在，pair 的成员变量 first 就是容器的结束迭代器，所以应该总是对它们进行捡查。

pair 的成员变量 second 也是一个迭代器，它指向键值大于参数的第一个参数；如果没有这样的元素，它会是一个结束迭代器。这段代码会输出容器中键值为”Ann”的元素的一些信息。

multimap 的成员函数 lower_bound() 会返回一个迭代器，它指向键值和参数相等或大于参数的第一个元素，或者指向结束迭代器。upper_bound() 也返回一个迭代器，它指向键值大于函数参数的第一个元素，如果这样的元素不出现的话，它就是一个结束迭代器。所以，当存在一个或多个相等键时，这些函数会返回一个开始迭代器和一个结束迭代器，它们指定了和参数匹配的元素的范围，这和 equal_range() 返回的迭代器是相同的。因而前面的代码段可以这样重写：

```
auto iter1 = people.lower_bound("Ann");
auto iter2 = people.lower_bound("Ann");
if(iter1 != std::end(people))
{
    for(auto iter = iterl ; iter != iter2; ++iter)
        std::cout << iter->first << " is " << iter->second << std::endl;
}
```

它和前一个代码段的输出结果是相同的。通过调用 multimap 的成员函数 count() 可以知道有多少个元素的键和给定的键相同。

```
auto n = people.count("Jack"); // Returns 2
```

可以用不同的方式使用这些函数。可以选择 find() 或 equal_range() 来访问元素。如果以班级为键，在 mutilmap 中保存学生信息，可以用成员函数 count() 来获取班级的大小。当然，通过将在前面章节介绍的 distance() 函数模板运用到成员函数 equal_range() 返回的迭代器或者 lower_bound() 和 upper_bound() 返回的迭代器上，也可以获取键和给定键相等的元素 的个数：

```
std::string key{"Jack"};
auto n = std::distance( people.lower_bound(key),people.upper_bound(key)); // No. of elements matching key
```

注意，全局的 equal_range()、lower_bound()、upper_bound() 函数模板的使用方式和关联容器中同名成员函数的使用方式略有不同。在教程后面的部分你会了解到这些。

multimap 的成员函数 erase() 有 3 个版本：

1.  以待删除兀素的迭代器作为参数，这个函数没有返回值；
2.  以一个键作为参数，它会删除容器中所有含这个键的元素，返回容器中被移除元素的个数；
3.  接受两个迭代器参数，它们指定了容器中的一段元素，这个范围内的所有元素都会被删除，这个函数返回的迭代器指向最后一个被删除元素的后一个位置。

下面在示例中尝试一些 multimap 操作：

```
// Using a multimap
#include <iostream>                                        // For standard streams
#include <string>                                          // For string class
#include <map>                                             // For multimap container
#include <cctype>                                          // For toupper()

using std::string;
using Pet_type = string;
using Pet_name = string;

int main()
{
    std::multimap<Pet_type, Pet_name> pets;
    Pet_type type {};
    Pet_name name {};
    char more {'Y'};
    while(std::toupper(more) == 'Y')
    {
        std::cout << "Enter the type of your pet and its name: ";
        std::cin >> std::ws >> type >> name;

        // Add element - duplicates will be LIFO
        auto iter = pets.lower_bound(type);
        if(iter != std::end(pets))
            pets.emplace_hint(iter, type, name);
        else
            pets.emplace(type, name);
        std::cout << "Do you want to enter another(Y or N)? ";
        std::cin >> more;
    }

    // Output all the pets
    std::cout << "\nPet list by type:\n";
    auto iter = std::begin(pets);
    while(iter != std::end(pets))
    {
        auto pr = pets.equal_range(iter->first);
        std::cout << "\nPets of type " << iter->first << " are:\n";
        for(auto p = pr.first; p != pr.second; ++p)
            std::cout << "  " << p->second;
        std::cout << std::endl;
        iter = pr.second;
    }
}
```

我们在代码中使用一些类型别名将类型及其表示的事物关联了起来。pets 容器保存的是 pair<string，string> 类型的对象，这个 pair 对象以 pet 类型作为键，以 pet 的名称为对象。

代码中的第一个循环，将给定键的第二个以及随后的元素插入到这个键序列的前面。这里使用 emplace_hint() 来插入元素。如果它是给定类型的第一个元素，就调用 emplace() 在适当的位置创建元素。

在第二个 while 循环中，按照 pet 的类型分组输出元素。首先找到 iter 指向的 pet 的第一个类型，然后用 equal_range() 返回的迭代器列出这种 pet 类型的全部序列。最后将 iter 设为这个序列的结束迭代器，它也是一个指向下一个 pet 类型的迭代器，或是容器的结束迭代器。后者会结束循环。下面是一些示例输出：

Enter the type of your pet and its name: rabbit Flopsy
Do you want to enter another(Y or N)? y
Enter the type of your pet and its name: rabbit Mopsy
Do you want to enter another(Y or N)? y
Enter the type of your pet and its name: rabbit Cottontail
Do you want to enter another(Y or N)? y
Enter the type of your pet and its name: dog Rover
Do you want to enter another(Y or N)? y
Enter the type of your pet and its name: dog Spot
Do you want to enter another(Y or N)? y
Enter the type of your pet and its name: snake Slither
Do you want to enter another(Y or N)? y
Enter the type of your pet and its name: snake Sammy
Do you want to enter another(Y or N)? n

Pet list by type:

Pets of type dog are:
  Spot  Rover

Pets of type rabbit are:
  Cottontail  Mopsy  Flopsy

Pets of type snake are:
  Sammy  Slither

输出表明元素是按键的升序排列的，键相同的元素的顺序和它们输入的顺序相反。