# C++ unordered_set 插入元素（insert 插入元素）详解

成员函数 insert() 可以插入作为参数传入的单个元素。在这种情况下，它会返回一个 pair 对象，这个 pair 对象包含一个迭代器，以及一个附加的布尔值用来说明插入是否成功。如果元素被插入，返回的迭代器会指向新元素；如果没有被插入，迭代器指向阻止插入的元素。可以用一个迭代器作为 insert() 的第一个参数，它指定了元素被插入的位置，如果忽略插入位置，在这种情况下，只会返回一个迭代器。另一个版本的 insert() 函数可以插入初始化表中的元素，在这种情况下，什么都没有返回。

下面是一些说明性语句：

```
auto pr = words.insert("ninety"); // Returns a pair - an iterator & a bool value
auto iter = words.insert (pr.first, "nine"); // 1st arg is a hint. Returns an iterator
words.insert({"ten", "seven", "six"});  // Inserting an initializer list
```

当调用 insert() 插入一段元素时，什么都不返回：

```
std::vector<string> more { "twenty", "thirty", "forty" };
words.insert(std::begin(more), std::end(more));//Insert elements from the vector
```

unordered_set 容器的成员函数 emplace() 和 emplace_hint() 可以在容器的适当位置创建元素。正如我们之前所见的 set 容器，传入 emplace() 的参数会被传入元素的构造函数，用来创建元素。emplace_hint() 的迭代器参数可以指定元素的插入位置，后面是构造元素需要的参数。例如：

```
std::unordered_set<std::pair<string, string>, Hash_pair> names;
auto pr = names.emplace ("Jack", "Jones") ; // Returns pair<iterator, bool>
auto iter = names.emplace_hint (pr.first, "John", "Smith"); // Returns an iterator
```

容器的元素是用来表示名称的 pair 对象，这里的每个名称由两个 string 对象组成，它们分别表示一个人的姓和名。unordered_set<T> 元素默认的哈希函数是一个 hash<T> 类模板的实例。这个模板对基本类型、指针、string 对象有一些特例化的定义。

因为没有 hash<pair<string，string>> 模板的特性化定义，所以需要定义一个哈希函数来哈希元素。这里将它的类型指定为 -Hash_pair，它也是模板的第二个类型参数。emplace() 的姓名参数会被传入 pair 的构造函数中，emplace_hint() 使用了一个指向先前插入元素的迭代器，这一点可能会被忽略。后面的参数是 pair 构造函数的参数。函数对象类型 Hash_pair 可以用来对 names 容器中的元素进行哈希，可以按如下方式定义它：

```
class Hash_pair
{
public:
    size_t operator()(const std::pair<string, string>& pr)
    {
        return std::hash<string>()(pr.first + pr.second);
    }
};
```

这里使用了一个定义在 string 头文件中的 hash<string> 函数对象的实例。它会哈希一个由 pair 对象的第一个成员和第二个成员串联的字符串，然后将结果作为这个 pair 元素的哈希值返回。