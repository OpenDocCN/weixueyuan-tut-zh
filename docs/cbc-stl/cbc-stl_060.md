# C++ unordered_map 插入元素（insert 插入元素）详解

unordered_map 容器的成员函数 insert() 提供的能力和 map 谷器的这个函数相同。可以通过复制或移动来插入一个元素，可以使用也可以不使用提示符来指明插入的位置。可以插入初始化列表中指定的元素或由两个迭代器指定范围内的元素。下面有一些示例，先看第一种情况：

```
std:: unordered_map<std:: string, size_t> people { {"Jim", 33}, { "Joe", 99}};// Name,age
std::cout <<"people container has " << people.bucket_count()<<" buckets.\n"; // 8 buckets
auto pr = people.insert (std::pair<string, size_t> {"Jan", 44});// Move insert
std:: cout << "Element " << (pr.second ? "was" : "was not") << " inserted." << std::endl;
```

第一条语句用两个初始元素生成了一个容器，它的格子个数是默认的。第二条语句调用 people 的成员函数 bucket_count() 来获取格子个数；注释中展示的是在笔者系统上执行代码后返回的值，但在你的系统上可能会有些不同。这个 insert() 调用是一个有右值引用参数的版本，所以 pair 对象会被移到容器中。这个函数返回了一个 pair 对象，它的成员变量 first 是一个迭代器，它指向插入的新元素；如果元素没有被插入，它指向的是阻止插入的元素。Pair 的成员变量 second 是一个布尔值，如果对象插入成功，它的值为 true。

看下面这些语句：

```
std::pair<std::string, size_t> Jim {"Jim", 47};
pr = people.insert(Jim);
std::cout << "\nElement " << (pr.second ? "was" : "was not") << " inserted. " << std::endl;
std::cout << pr.first->first << " is " « pr.first->second << std::endl;
// 33
```

因为参数是左值，所以会调用有 const 引用参数的 insert() 版本，如果插入成功，它会将元素复制到容器中。这个插入操作不会成功，因为容器中已经有键值为 string("Jim") 的元素，所以最后一条语句输出的年龄为 33。

```
auto count = people.size();
std::pair<std::string, size_t> person {"Joan", 33};
auto iter = people.insert(pr.first,person);
std::cout << "\nElement." << (people.size () > count ? "was" : "was not") << " inserted." << std::endl;
```

这里，insert() 的第一个参数是一个迭代器，它是上一个 insert() 调用返回的 pair 对象的成员变量 first，用来作为标识元素插入位置的指示符;容器不一定会使用这个指示符。insert 的第二个参数是待插入的元素。这个版本的 insert() 函数不会返回一个 pair 对象，但会返回一个指向插入元素或阻止插入操作的元素的迭代器。这段代码中使用容器的成员函数 size() 返回的元素个数来判断元素是否插入成功。

也可以插入初始化列表中的内容：

```
people.insert({{"Bill", 21}, {"Ben", 22}});//Inserts the two elements in the list
```

这个版本的 insert() 不会有返回值，可以插入元素段的 insert() 版本也没有返回值：

```
std::unordered_map<std::string, size_t> folks; // Empty container
folks.insert(std::begin(people), std::end(people));// Insert copies of'all people elements
```

迭代器所定义的来自于 people 容器的元素和 folks 中的元素是同种类型，people 可以是任意类型的容器，只要它的元素类型符合 folks 的要求。

可以调用 unordered_map 容器的成员函数 emplace() 或 emplace_hint() 在容器的适当位置生成元素。例如：

```
auto pr = people. emplace ("Sue", 64);// returns pair<iterator, bool>
auto iter = people.emplace_hint(pr.first, "Sid", 67);// Returns iterator
people.emplace_hint(iter, std::make_pair("Sam", 59));//Uses converting pair<string, size_t>
```

emplace() 可以用参数在容器的适当位置生成对象，它会返回一个包含迭代器和布尔值的 pair 对象，这和 insert() 返回的 pair 对象有同样的意义。emplace_hint() 的第一个参数是一个作为提示符的迭代器，后面的参数被用来生成元素，它返回的迭代器指向被插入元素或阻止插入的元素。

为了可以用 unordered_map 对象的内容替换这个容器的内容，unordered_map 容器实现了赋值运算符；

```
folks = people; // Replace folks elements by people elements
```

显然，参数所包含的元素的类型必须和当前容器相同。

## 调整格子个数

在维持当前载入因子的前提下，如果插入元素数超过了格子可以满足的个数，容器将不得不增加格子的个数。那么为了将元素重新分配到新的格子中，元素会被再次哈希。这时，这个容器当前存在的任何迭代器都会失效。在任何时候都可以调用成员函数 rehash() 来改变格子的个数：

```
people.rehash(15); // Make bucket count 15
```

rehash() 的参数可以比当前格子数多或少。这条语句会将格子的个数变为 15，只要它不导致当前因子超过最大载入因子。容器中的所有元素都会被重新哈希分配到新的格子中，而且当前所有的迭代器都会失效。如果指定的格子个数导致载入因子超过最大载入因子，那么格子会自动增加来避免超出最大值。

如果确定会增加格子的个数，可以使用 bucket_count() 返回的值：

```
people.rehash((5*people.bucket_count())/4); // Increase bucket count by 25%
```

另一种方式是增加最大载入因子，也就是增加每个格子所包含的元素个数：

```
people.max_load_factor(1.2*people.max_load_factor()); // 工 ncrease max load factor by 20%
```

为了改变最大载入因子，可以以新的最大载入因子为参数调用容器的 max_load_factor(); 如果无参数地调用这个函数，它会返回当前的最大载入因子，可以用它来设置新的值。

可以发现，调用 unordered_map 的成员函数 load_factor() 时返回的当前载入因子是一个浮点值：

```
float lf = people.load_factor();
```

也可以选择设置格子的个数，使它们在容纳给定个数的元素的同时将负载因子维持在最大数之内：

```
size_t max_element_count {100};
people.reserve(max_element_count);
```

这里设置了格子的个数，使它可以容纳 100 个元素而不超过最大载入因子的限制。这会导致容器中的内容被重新哈希，从而使所有的当前迭代器失效。当然，也可以不考虑载入因子和格子个数来生成和使用 unordered_map 容器。容器自己会处理这些事情。

对于现实世界中的应用来说，性能是很重要的，而容器就是影响性能的一个重要因素。当每个格子中的元素不超过一个时，访问速度是最快的，但实际上这是不现实的，因为这需要很多内存而且会有很多的空格子。增大最大载入因子可以使每个格子容纳更多的元素，从而使格子的总数越少。所以从内存使用上来说，这是最有效率的。然而，每个格子中的元素越多，会导致访问元素的速度越慢。这需要根据每个程序的具体情况来选择。或许最重要的事是要避免反复哈希容器的内容。如果可以预估出要保存的元素的个数，就可以设定格子的个数或者设定合适的载入因子，从而减少再次哈希的可能。