# C++ 自定义比较函数（map 和 multimap）详解

对于为什么要改变 map 或 multimap 的比较函数，可能想用降序排列的元素来代替默认升序排列的元素；或者键需要使用的比较函数和直接的小于或大于运算符不同。例如，如果键是指针的话，就需要使用这种函数。在看一个演示如何替代比较函数的示例之前，先强调比较键的函数对象所需要的一个非常重要的条件。

需要注意的是，map 容器的比较函数在相等时不能返回 true，换句话说，不能使用<=或>=来比较。这是为什么？ map 或 multimap 容器用等价来判 断键是否相等。如果表达式 key1<key2 和 key2<key1 的结果都是 false，那么 key1 和 key2 是等价的，所以它们被认为是相等的。换一种方式，等价意味着 !(key1<key2)&&!(key2<key1) 的运算值为 true。

如果我们的函数对象实现了 <=，思考一下会发生什么。当 key1 和 key2 相等时，key1<=key2 和 key2<=key1 都为 true，因而表达式 !(key1<=key2)&&!(key2<=key1) 为 false，这意味着来自于容器的这两个键竟然不相等。

事实上，不会出现用相等来判断键的情况。这意味着这个容器将无法进行正常操作。让我们来看一下如何替换比较函数，从而使容器可以正常操作。

## greater<T>对象的用法

假设我们已经为先前章节中使用的 Name 类实现了 operator>()。在这个类的定义中，成员函数 operator>() 的定义如下：

```
bool operator>(const Name& name) const
{
    return second > name.second ||(second == name.second && first > name.first);
}
```

当然，可以将成员函数的定义放在类的外面：

```
inline Name::bool operator> (const Name& name) const
{
    return second > name.second || (second == name.second && first > name.first);
}
```

现在可以用 Name 对象作为 map 的键，将容器中的 pair 对象按降序排列：

```
std::map<Name,size_t, std::greater<Name>>people{{Name{"Al", "Bedo"}, 53}, {Name{"Woody","Leave"},33},{Name{"Noah", "Lot"}, 43}};
```

这里第三个模板类型参数指定了用来比较键的函数对象的类型。greater<Name> 对象使用 > 运算符来比较 Name 对象，因为 Name 类实现了 operator>()，所以可以这样做。这三个元素会按照降序排列。如果列出这些元素，它们的排列方式是很明显的，可以像下面这样列出元素：

```
for ( const auto& p : people)
    std:: cout << p.first << " " << p.second << " \n";
```

基于范围的 for 循环遍历了 people 容器中的所有元素，然后将它们输出：

Noah Lot 43
Woody Leave 33
Al Bedo 53

## 用自定义的函数对象来比较元素

如果 map 或 multimap 中的键是指针的话，那么需要定义一个函数来比较它们所指向的对象，否则会比较指针所表示的地址，这并不是我们想要的。如果键是不支持直接进行 < 或 > 比较的类型，为了可以在 map 或 multimap 中使用它们，必须为它们定义一个适当的函数对象。处理这两种情况的方式在本质上相同。

假设我们想用堆上生成的对象的指针作为 map 容器的键。下面用指向 string 对象的智能指针来说明这种情况。这个容器的键可以是 unique_ptr<string> 类型，在这个示例中我们需要含两个 unique_ptr<string> 参数的比较函数，这个函数可以比较它的参数所指向的 string 对象。可以用伪函数，即函数对象来定义它，这里假设使用了 using_std::string：

```
// Compares keys that are unique_ptr<string> objects
class Key_compare
{
public:
    bool operator () (const std::unique_ptr<string>& p1, const std::unique_ptr <string>& p2) const
    {
        return *p1 < *p2;
    }
};
```

可以用 Key_compare 类型作为 map 容器来比较键的函数对象的类型：

```
std::map<std::unique_ptr<string>,std::string,Key_compare> phonebook;
```

第三个 map 模板参数指定了用来比较元素的函数对象的对象，因为这个类型参数指定了默认的值 less<T>，所以我们需要指定我们自己定义的函数对象。map 中的元素是 pair 对象，它封装了一个指向 string 对象的智能指针，string 对象用来保存人名和电话。对于这个 map，我们不能使用初始化列表，因为初始化列表包含了副本，而 unique_ptr 对象是不能被复制的。我们至少有两种向容器中添加元素的方式：

```
Phonebook.emplace(std::make_unique<string>("Fred"), "914 626 7897");
Phonebook.insert(std::make_pair(std::make_unique<string>("Lily"), "212 896 4337"));
```

第一条语句在容器的适当位置直接生成了一个 pair 对象。这个 pair 对象的构造函数可以移动这里指定的参数，所以可以无条件地复制它们。第二条语句调用了容器的成员函数 insert()，它会将参数元素移到容器中。

可以按如下所示列出 phonebook 容器中的元素：

```
for (const auto& p: phonebook)
    std:: cout << *p.first << " " << p.second << std::endl;
```

基于范围的 for 循环遍历了 map 中的所有元素，map 中的元素都是 pair 对象。每一个 pair 对象的成员变量 first 都是 unique 指针，所以不得不通过解引用来访问它们所指向的 string 对象。如果使用迭代器来访问元素，语法略有不同：

```
for (auto iter = std::begin (phonebook) ;iter != std:: end (phonebook) ; ++iter)
    std:: cout << *iter->first << " " << iter->second << std::endl;
```

它和之前的循环有相同的输出，但使用的是迭代器。可以用 -> 运算符来访问 pair 对象的成员。因为这样定义了伪函数 Key_compare，所以容器中的元素会被升序排列。