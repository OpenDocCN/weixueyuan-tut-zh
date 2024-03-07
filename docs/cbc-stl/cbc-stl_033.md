# C++ list（STL list）增加和插入元素详解（insert 成员函数插入元素）

可以使用 list 容器的成员函数 push_front() 在它的头部添加一个元素。调用 push_back() 可以在 list 容器的末尾添加一个元素。在下面这两个示例中，参数作为对象被添加：

```
std::list<std::string> names { "Jane", "Jim", "Jules", "Janet"};
names.push_front("Ian"); // Add string ("Ian") to the front of the list
names.push_back("Kitty"); // Append string ("Kitty") to the end of the list
```

这两个函数都有右值引用参数的版本，这种版本的函数会移动参数而不是从参数复制新的元素。它们显然要比其他使用左值引用参数的版本高效。然而，成员函数 emplace_front() 和 emplace_back() 可以做得更好：

```
names.emplace_front("Ian");//Create string ("Ian") in place at the front of the list
names.emplace_back("Kitty");// Create string ("Kitty") in place at the end of the list
```

这些成员函数的参数是调用以被生成元素的构造函数的参数。它们消除了 pushJfront() 和 push_back() 不得不执行的右值移动运算。

可以使用成员函数 insert() 在 list 容器内部添加元素。像其他序列容器一样，它有三个版本。

1) 可以在迭代器指定的位置插入一个新的元素：

```
std::list<int> data(10, 55); // List of 10 elements with value 55
data.insert(++begin(data), 66); // Insert 66 as the second element
```

insert() 的第一个参数是一个指定插入点的迭代器，第二个参数是被插入的元素。begin() 返回的双向迭代器自增后，指向第二个元素。上面的语句执行完毕后，list 容器的内容如下：
55 66 55 55 55 55 55 55 55 55 55

list 容器现在包含 11 个元素。插入元素不必移动现有的元素。生成新元素后，这个过程需要将 4 个指针重新设为适当的值。第一个元素的 next 指针指向新的元素，原来的第二个元素的 pre 指针也指向新的元素。新元素的 pre 指针指向第一个元素，next 指针指向序列之前的第二个元素。和 vector、deque 容器的插入过程相比，这个要快很多，并且无论在哪里插入元素，花费的时间都相同。

2) 可以在给定位置插入几个相同元素的副本：

```
auto iter = begin(data);
std::advance(iter, 9); // Increase iter by 9
data.insert(iter, 3, 88);// Insert 3 copies of 88 starting at the 10th
```

iter 是 list<int>::iterator 类型。insert() 函数的第一个参数是用来指定插入位置的迭代器，第二个参数是被插入元素的个数，第三个参数是被重复插入的元素。为了得到第 10 个元素，可以使用定义在 iterator 头文件中的全局函数 advance()，将迭代器增加 9。只能增加或减小双向迭代器。因为迭代器不能直接加 9，所以 advance() 会在循环中自增迭代器。

执行完上面的代码后，list 容器的内容如下：
55 66 55 55 55 55 55 55 55 88 88 88 55 55

现在 list 容器包含 14 个元素。下面展示如何将一段元素插入到 data 列表中：

```
std::vector<int> numbers(10, 5)/ // Vector of 10 elements with value 5
data.insert(--(--end(data)), cbegin(numbers), cend(numbers));
```

insert() 的第一个参数是一个迭代器，它指向 data 的倒数第二个元素。第二和第三个参数指定了 number 中被插入元素的范围，因此从 data 中倒数第二个元素开始，依次插入 vector 的全部元素。代码执行后，data 中的内容如下：
55 66 55 55 55 55 55 55 55 88 88 88 5 5 5 5 5 5 5 5 5 5 55 55

list 容器现在有 24 个元素。从 numbers 中倒数第二个元素的位置开始插入元素，list 最右边两个元素的位置被替换了。尽管如此，指向最后两个元素的任何迭代器或结束迭代器都没有失效。list 元素的迭代器只会在它所指向的元素被删除时才会失效。

3) 有三个函数可以在 list 容器中直接构造元素：emplace() 在迭代器指定的位置构造一个元素；emplace_front() 在 list 的第一个元素之前构造元素；emplace_back() 在 list 的尾部元素之后构造元素。下面是一些用法示例：

```
std::list<std:: string> names {"Jane", "Jim", "Jules", "Janet"};
names.emplace_back("Ann");
std:: string name ("Alan");
names.emplace_back(std::move(name));
names.emplace_front("Hugo");
names.emplace(++begin(names), "Hannah");
```

第 4 行代码用 std::move() 函数将 name 的右值引用传入 emplace_back() 函数。这个操作执行后，names 变为空，因为它的内容已经被移到 list 中。执行完这些语句后，names 中的内容如下：
"Hugo” "Hannah" "Jane" "Jim" "Jules" "Janet" "Ann" "Alan"