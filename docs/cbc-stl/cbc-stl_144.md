# C++重载插入运算符（<<）和提取运算符(>>)详解

必须为任何想和输出流迭代器一起使用的类类型重载插入和提取运算符。对于自己的类，这是很容易的事。必要的话，可以提供 get 和 set 函数来访问任何 private 或 public 数据成员，或者将运算符函数指定为友元函数。下面是一个简单的示例，有一个表示姓名的类可以说明：

```
class Name
{
private:
    std::string first_name{};
    std::string second_name{};
public:
    Name() = default;
    Name(const std::strings first, const std::strings second):first_name{first}, second name {second} {}
    friend std::istream& operator>>(std::istream& in, Name& name);
    friend std::ostream& operator<<(std::ostream& out, const Name& name);
}；
// Extraction operator for Name objects
inline std::istream& operator>>(std::istream& in, Name& name)
{ return in >> name.first_name >> name.second_name; }

//Insertion operator for Name objects
inline std::ostream& operator<<(std::ostream& out, const Name& name)
{ return out << name.first_name << ' ' << name.second_name; }
```

这里定义了运算符的重载，可以像这个示例一样，用流迭代器来读和写 name 对象:

```
std::cout << "Enter names as first_name second_name. Enter Ctrl+Z on a separate line to end:\n";
std::vector<Name> names {std::istream_iterator<Name> {std::cin},std::istream_iterator<Name>{}};
std::copy (std::begin (names),std::end (names) , std::ostream_iterator<Name> {std::cout," "});
```

names 容器会被输入的 Name 对象初始化，直到输入了 Ctrl+Z 来结束输入。copy() 算法会将 Name 对象复制到输出流迭代器标识的目的位置，这个迭代器会将对象写到标准输出流中。我们这里的名和姓会阻止姓名按指定的宽度列对齐。例如，下面代码的效果就不会很好：

```
for(const auto& name: names)
    std::cout << std::setw(20) << name << std::endl;
```

有个主意，就是在单一的列中输出姓名。这个效果也不理想，因为指定的宽度只会被应用到成员 first_name 上。可以通过改变 operator<<() 来做到这一点，因此在输出姓名之前， 会先将它们连接起来：

```
inline std::ostream& operator<<(std::ostream& out, const Name& name)
{ return out << name.first_name + ' ' + name.second_name; }
```

这没有原始的 << 高效，因为这种方式会生成临时的 string 对象，但它能够使前面的循环按要求工作。

有时候，可能会想以不同的方式来输出，这取决于目的位置是不是一个文件。例如，我们可能会想在标准输出流的输出中包含一些在将它们写入文件时不想包含的额外信息。这可以通过检查 ostream 对象的实际类型来做到：

```
inline std::ostreams operator<<(std::ostream& out, const Names name)
{
    if(typeid(out) != typeid(std::ostream))
        return out << name.first_name << " " << name.second_name;
    else
        return out << "Name: " << name.first_name << ' ' << name.second_name;
}
```

现在，当它被写到 ostream 对象的流时，name 会被带上前缀 "Name: "。在输出到文件流或从 ostream 派生的其他流类型中，前缀会被省略。