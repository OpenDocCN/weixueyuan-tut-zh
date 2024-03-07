# C++ map 获取（访问）元素详解

我们已经知道，可以获取 map 容器的开始和结束迭代器以及反向迭代器，它们都可以访问容器中的所有元素。map 的成员函数 at() 返回的是参数键对应的对象。如果这个键不存在，就会拋出 out_of_range 异常。下面展示如何使用这个函数：

```
Name key;
try
{
    key = Name {"Dan”, ”Druff"};
    auto value = people.at(key);
    std:: cout << key << "is aged " << value << std:: endl;
    key = Name {"Don", "Druff"};
    value = people.at(key);
    std::cout << key << " is aged " << value << std::endl;
}
catch(const std::out_of_range& e)
{
    std::cerr << e.what() << '\n'<< key << " was not found." <<std::endl;
}
```

需要在 try 代码块中调用 map 的成员函数 at()，因为抛出的任何未捕获的异常都会导致程序的终止。这段代码获取了 people 容器中的两个对象，它们分别与两个 Name 键关联。如果 map 容器中的内容由执行的前一节中的代码段决定，输出效果如下：

Dan Druff is aged 77
invalid map<K, T> key
Don Druff was not found.

Try 代码块中第一次调用 at() 函数成功，结果会在首行输出。第二次调用失败，拋出了一个可捕获的 out_0f_range 异常，捕获结果在后面两行输出。异常对象的成员函数 what() 是一个返回了描述异常产生原因的字符串。当 catch 代码块中的代码执行后，try 代码块中的所有变量会被销毁，因此不再可以访问。变量 key 是在 try 代码块之前定义的，因此仍然可以在 catch 代码块中访问。

map 容器提供了以键为参数的下标运算符，它可以返回一个和键所关联对象的引用。下面是一个示例：

```
auto age = people [Name {"Dan", "Druff”}];
```

这里获取到一个和 Name 键关联的 size_t 类型的值。注意，下标运算的使用并不是简单的检索机制。如果键不存在，元素默认的构造函数会用键和键所关联的对象生成一个新元素，如果键关联的对象是基本数据类型，它的值为 0。例如：

```
auto value = people[Name {"Ned", "Kelly"}]; // Creates a new element if the key is not there
```

因为容器中不存在这个键，所以用它生成了新元素。关联对象的值是 0，并会返回这个值。可以用下标运算符来更新 map 中的元素，如果元素不在 map 中，也可以用它插入元素。下标运算主要用在左赋值上，用来修改已存在的元素：

```
people[Name {"Ned", "Kelly”}] = 39; // Sets the value associated with the key to 39
```

让我们在新的示例中，用一种不同以往的方式使用 map，并且充分利用下标运算符。可以用 map 容器来确定每个字符在文本中出现的频率。确定词频是非常有用的，例如，可以用它对文档进行分类。下面展示了如何在任意文本序列中统计每个单词的出现次数：

```
// Determining word frequency
#include <iostream>                               // For standard streams
#include <iomanip>                                // For stream manipulators
#include <string>                                 // For string class
#include <sstream>                                // For istringstream
#include <algorithm>                              // For replace_if() & for_each()
#include <map>                                    // For map container
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

    std::map<string, size_t> words;               // Map to store words & word counts
    size_t max_len {};                            // Maximum word length

    // Get the words, store in the map, and find maximum length
    std::for_each(begin, end, &max_len, &words
                            {  words[word]++;
                               max_len = std::max(max_len, word.length());
                            });

    size_t per_line {4}, count {};
    for(const auto& w : words)
    {
        std::cout << std::left << std::setw(max_len + 1) << w.first << std::setw(3) << std::right << w.second << "  ";
        if(++count % per_line == 0)  std::cout << std::endl;
    }
    std::cout << std::endl;
}
```

从标准输入流读取到 text_in 中的文本是通过函数 getline() 得到的字符串。replace_if() 算法用空格替换了输入中的所有非字母字符。replace_if() 函数的前两个参数是定义元素范围的迭代器，这里的元素范围就是输入字符串的字符。下一个参数是一个函数对象，当元素需要被替换时，它返回 true；这里是一个 lambda 表达式。最后一个参数是用来替换的元素，在这个示例中这个元素是空格。这个函数会替换掉所有的标点，所以最后每个元素都是用空格分隔的。

我们用 text_in 生成一个 istringstream 对象 text。istringstream 对象允许对它封装的字符串进行流输入操作，因此可以把它当作一个流。这也包括从 text 获得流迭代器的能力，然后可以在 for_each() 中用它们提取单个单词。输入流的迭代器会陆续指向每个输入的字符串。这里输入的单词是连续的，因此开始和结束迭代器指定的范围是 text 中的所有单词。

for_each() 会将第 3 个参数指定的函数对象运用到前两个参数所指定范围内的元素上。函数对象必须以迭代器指向对象类型的引用作为参数，所以这里参数是 const string &。lambda 以引用的方式捕获变量 max_len 和 words，所以它们都可以修改。lambda 通过将每个单词作为下标来将它们以键的方式保存在容器中，并增加单词关联的值。如果单词不在容器中，会以这个单词为键(值为 1)来生成一个新的元素。如果单词先前就被添加到容器中，就自动增加值。因此与每个单词的关联值就是它在文本中累计出现的次数。为了保存最长字符串的长度，lambda 表达式也会更新 max_len。后面的输出中会用到这个值。

因而调用 for_each() 会将输入的所有单词都插入到这个 map 容器中，并且累加计算出每个单词的出现次数，计算出最大单词的长度，一条语句就实现了上面这些功能。

下面是程序输出的结果：

Enter some text and enter * to end:
How much wood would a wood chuck chuck,
If a woodchuck could chuck wood?
A woodchuck would chuck as much wood as a woodchuck could chuck if a woodchuck could chuck wood.
*
A         1  How    1 If                 1 a          4
as        2 chuck  6 could           3 if          1
much   2 wood   5 woodchuck 4 would   2

在这个示例中，map 容器中保存的是整型对象，所以可以对容器的下标运算符返回的值运用自增运算符。当 map 的下标运算符返回的值是类类型的对象时，也可以对它们使用运算符，只要这个类实现了对应的运算符。为了说明我们所讨论的这种情况，下面创建另一个示例。

假设我们要通过人名来保存并检索名人名言。显然，一个名人会有很多名言，因此我们需要通过单个键来保存多个名言。我们不能在 map 容器中保存重复的键，但是可以将键关联到封装了多个名言的对象上。我们可以用前面章节中的 Name 类作为键，然后定义 Quotations 类用来保存指定名人的所有名言。

我们知道，可以用键的下标运算符来访问和键关联的对象，因此可以通过扩展 Quotations 类的成员函数 operator[]() 来实现这个功能。为了方便向 Quotation 类中添加名言，我们还在类中实现了 operator<<0。我们可以方便地将名言保存在 vector 容器中。下面就是定义了这个类的 Quotations.h 头文件的内容：

```
#ifndef QUOTATIONS_H
#define QUOTATIONS_H
#include <vector>                                          // For vector container
#include <string>                                          // For string class
#include <exception>                                       // For out_of_range exception

class Quotations
{
private:
    std::vector<std::string> quotes; // Container for the quotations

public:
    // Stores a new quotation that is created from a string literal
    Quotations& operator<<(const char* quote)
    {
        quotes.emplace_back(quote);
        return *this;
    }

    // Copies a new quotation in the vector from a string object
    Quotations& operator<<(const std::string& quote)
    {
        quotes.push_back(quote);
        return *this;
    }

    // Moves a quotation into the vector
    Quotations& operator<<(std::string&& quote)
    {
        quotes.push_back(std::move(quote));
        return *this;
    }

    // Returns a quotation for an index
    std::string& operator[](size_t index)
    {
        if(index < quotes.size())
            return quotes[index];
        else
            throw std::out_of_range {"Invalid index to quotations."};
    }

    size_t size() const// Returns the number of quotations
    {
        return quotes.size();
    }

    // Returns the begin iterator for the quotations
    std::vector<std::string>::iterator begin()
    {
        return std::begin(quotes);
    }

    // Returns the const begin iterator for the quotations
    std::vector<std::string>::const_iterator begin() const
    {
        return std::begin(quotes);
    }

    // Returns the end iterator for the quotations
    std::vector<std::string>::iterator end()
    {
        return std::end(quotes);
    }

    // Returns the const end iterator for the quotations
    std::vector<std::string>::const_iterator end() const
    {
        return std::end(quotes);
    }
};
#endif
```

这里用 << 运算符来添加名言是合理的，它可以在其他一些场景下使用，例如输入流。这里也可以用 += 运算符来代替。这个类定义了 3 个版本的 operator<<()，提供了不同的方式去添加名言。第一个版本接收一个字符串常量参数，然后把它传给 vector 的成员函数 emplace_back()，emplace__back() 会调用 string 的构造函数以在适当的位置生成元素。第二个版本只有一个参数，它是 string 对象的引用，这个参数会被传给 vector 的成员函数 push_back()。第三个版本有一个右值引用参数。当在函数体中通过名称使用右值引用时，它会变成左值，因此必须使用 move() 函数将它变为右值，然后把它传给 vector 的成员函数 push_back()。这会保证对象总是移动传值，而不是复制传值。

类的成员函数 []() 可以通过索引来访问成员元素。当索引不在范围内时，这个函数将抛出一个异常，这种情况不应该发生；如果真的发生，这会是程序中的一个 bug。

在 vector 容器中，begin() 和 end() 返回指向名言的迭代器。需要注意的是，返回类型是指定的。提供迭代器的容器通常会定义一个迭代器成员变量，作为它们支持的迭代器类型的别名，所以不需要知道类型的具体细节。类对象定义的迭代器可以结合 for 循环使用，但要求迭代器至少是正向迭代器。

在 Quotations 类中也定义了 const 版本的 begin() 和 end()，它们的返回值都是 const 类型的迭代器。这个返回类型有一个别名，定义在 vector 模板中。如果没有定义 const 版的 begin() 和 end() 函数，就不能在 for 循环中使用 const 类型的循环变量，例如：

```
for (const auto& pr : quotations)//Requires const iterators
    ...
```

可以在 main() 中定义两个内联辅助函数。第一个用来从 cin 读入 name:

```
inline Name get_name()
{
    Name name {};
    std: :cout << "Enter first name and second name: ";
    std::cin >>std::ws >> name;
    return name;
}
```

这里读取的 name 用来作为名和姓。控制符 ws 用来消除空格，因此会跳过 cin 中剩下的字符。 第二个辅助函数用来读取名言：

```
inline string get_quote(const Name& name)
{
    std::cout << "Enter the quotation for " << name << ".Enter * to end: \n";
    string quote;
    std::getline(std::cin >> std::ws, quote, '*');
    return quote;
}
```

可以输入多行文本，然后用 * 号终止输入。下面的程序支持保存名言：

```
// Stores one or more quotations for a name in a map
#include <iostream>                              // For standard streams
#include <cctype>                                // For toupper()
#include <map>                                   // For map containers
#include <string>                                // For string class
#include "Quotations.h"
#include "Name.h"

using std::string;

// Read a name from standard input
inline Name get_name()
{
    Name name {};
    std::cout << "Enter first name and second name: ";
    std::cin >> std::ws >> name;
    return name;
}

// Read a quotation from standard input
inline string get_quote(const Name& name)
{
    std::cout << "Enter the quotation for " << name
    << ". Enter * to end:\n";
    string quote;
    std::getline(std::cin >> std::ws, quote, '*');
    return quote;
}

int main()
{
    std::map<Name, Quotations> quotations;         // Container for name/quotes pairs

    std::cout << "Enter 'A' to add a quote."
    "\nEnter 'L' to list all quotes."
    "\nEnter 'G' to get a quote."
    "\nEnter 'Q' to end.\n";
    Name name {};                                  // Stores a name
    string quote {};                               // Stores a quotation
    char  command {};                              // Stores a command

    while(command != 'Q')
    {
        std::cout << "\nEnter command: ";
        std::cin >> command;
        command = static_cast<char>(std::toupper(command));
        switch(command)
        {
            case 'Q':
                break;                                     // Quit operations

            case 'A':
                name = get_name();
                quote = get_quote(name);
                quotations[name] << quote;
                break;

            case 'G':
            {
                name = get_name();
                const auto& quotes = quotations[name];
                size_t count = quotes.size();
                if(!count)
                {
                    std::cout << "There are no quotes recorded for "<< name << std::endl;
                    continue;
                }
                size_t index {};
                if(count > 1)
                {
                    std::cout << "There are " << count << " quotes for " << name << ".\n"<< "Enter an index from 0 to " << count - 1 << ": ";
                    std::cin >> index;
                }
                std::cout << quotations[name][index] << std::endl;
            }
            break;
            case 'L':
            if(quotations.empty())                                         // Test for no pairs
            {
                std::cout << "\nNo quotations recorded for anyone." << std::endl;
            }
            // List all quotations
            for(const auto& pr : quotations)                               // Iterate over pairs
            {
                std::cout << '\n' << pr.first << std::endl;
                for(const auto& quote : pr.second)                           // Iterate over quotations
                {
                    std::cout << "  " << quote << std::endl;
                }
            }
            break;
            default:
                std::cout << " Command must be 'A', 'G', 'L', or 'Q'. Try again.\n";
                continue;
                break;
        }
    }
}
```

quotations 容器保存的是 pair<constName, Quotations> 对象类型的元素。像 quotations[name] 这种表达式可以引用 Name 对象 name 关联的对象。如果在 map 中不存在和键值 name 关联的 pair 对象，就用默认关联的 Quotations 对象生成一个 pair 对象，默认的 Quotations 对象为空。下面的语句会为 name 保存一条新的名言 quote:

```
quotations[name] << quote;
```

<< 左边的操作数等同于 quotations.operator[](name)，它返回一个和 name 关联的 Quotations 对象，因此这条语句等价于：

```
quotations.operator[](name).operator<<(quote);
```

在 main() 函数中可以看到，我们利用表达式 quotations[name][index] 来得到一条名言，它等价于 quotations.operator[](name).operator[](index)，你应该知道 main() 剩下的代码是如何工作的，下面就是一些示例输出：

Enter 'A' to add a quote.
Enter 'L' to list all quotes.
Enter 'G' to get a quote.
Enter 'Q' to end.

Enter command: a
Enter first name and second name: Winston Churchill
Enter the quotation for Winston Churchill. Enter * to end:
There are a terrible lot of lies going around the world, and the worst of it is half of them are true.*

Enter command: a
Enter first name and second name: Dorothy Parker
Enter the quotation for Dorothy Parker. Enter * to end:
Beauty is only skin deep,but ugly goes clean to the bone.*

Enter command: a
Enter first name and second name: Winston Churchill
Enter the quotation for Winston Churchill. Enter * to end:
Never in the field of human conflict was so much owed by so many to so few.*

Enter command: a
Enter first name and second name: Winston Churchill
Enter the quotation for Winston Churchill. Enter * to end:
Courage is what it takes to stand up and speakm,Courage is also what it takes to sit down and listen.*

Enter command: a
Enter first name and second name: Dorothy Parker
Enter the quotation for Dorothy Parker. Enter * to end:
Money cannot buy health,but I'd settle for a diamond-studded wheelchair.*

Enter command: g
Enter first name and second name: Winston Churchill
There are 3 quotes for Winston Churchill.
Enter an index from 0 to 2: 1
Never in the field of human conflict was so much owed by so many to so few.

Enter command: L

Winston Churchill
  There are a terrible lot of lies going around the world, and the worst of it is half of them are true.
  Never in the field of human conflict was so much owed by so many to so few.
  Courage is what it takes to stand up and speakm,Courage is also what it takes to sit down and listen.

Dorothy Parker
  Beauty is only skin deep,but ugly goes clean to the bone.
  Money cannot buy health,but I'd settle for a diamond-studded wheelchair.

Enter command: q

显然，这个程序可以有更好的容错能力，也可以支持忽略大小的键值比较，这取决于你的想法。

map 容器的成员函数 fmd() 可以返回一个元素的迭代器，这个元素的键值和参数匹配。例如：

```
std::map<std::string, size_t> people {{"Fred", 45}, {"Joan", 33}, {"Jill", 22}};
std::string name{"Joan"};
auto iter = people.find(name);
if(iter == std::end(people))
    std:: cout <<"Not found.\n";
else
    std:: cout << name << " is ""<< iter->second << std::endl;
```

如果没有和参数匹配的元素，find()函数会返回容器的结束迭代器，因此在使用这个迭代器之前，必须先对它进行检查。

为了兼容 multimap，map 容器包含了成员函数 equal_range(}、upper_bound()和 lower_ bound()，因为这些函数会用来查找具有相同键的多个元素。稍后在 multimap 容器这一节中对它们进行深入讲解。