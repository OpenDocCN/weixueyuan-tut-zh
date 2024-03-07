# C++ map（STL map）构造元素（emplace 和 emplace_hint 函数）详解

map 容器的成员函数 emplace() 可以在适当的位置直接构造新元素，从而避免复制和移动操作。它的参数通常是构造元素，也就是 pair<const K,T> 对象所需要的。只有当容器中现有元素的键与这个元素的键不同时，才会构造这个元素。下面是一个示例：

```
std::map<Name, size_t> people;
auto pr = people.emplace (Name { "Dan","Druff"},77);
```

这个 map 包含的是 Name 类型(前面章节中定义过)的键。对象的类型是 size_t，所以 map 包含的是 pair<const Name,size_t> 类型的元素。emplace() 的第一个参数是一个 Name 对象，它同时也是元素的键，第二个参数是 size_t 类型的值，函数会用这些参数调用 pair<const Name,size_t> 的构造函数以在适当的位置生成元素。如果用 emplace() 的参数构造 pair 对象，那么会调用 pair<const Name，size_t> 对象的移动构造函数。

成员函数 emplace() 和 insert() 返回的 pair 对象提供的指示相同。pair 的成员变量 first 是一个指向插入元素或阻止插入的元素的迭代器；成员变量 second 是个布尔值，如果元素插入成功，second 就为 true。

map 的成员函数 emplace_hint() 和 emplace() 生成元素的方式在本质上是一样的，除了必须为前者提供一个指示元素生成位置的迭代器，作为 emplace_hint() 的第一个参数。例如：

```
std::map<Name, size_t> people;
auto pr = people.emplace(Name{"Dan","Druff"}, 77);
auto iter = people.emplace_hint (pr.first, Name {"Cal","Cutta"}, 62);
```

这里，emplace_hint() 调用使用一个迭代器作为指示符，指向先前 emplace() 调用返回的 pair 对象。如果容器使用这个提示符，那么新元素会在这个指示符表示的位置之前生成，并尽可能靠近这个位置。提示符后面的参数用来构造新元素。需要注意的是，它和 emplace() 的返回值是不一样的。emplace_hint() 的返回值不是一个 pair 对象，如果新元素被插入，它返回的是指向新元素的迭代器；如果没有插入，返回的是和这个键匹配的现有元素的迭代器，拥有相同的 key 值，如果不是现有元素的话。没有提示可以直接判断是否生成了新元素。唯一的方法是，用 size() 成员函数来获取 map 中对应元素的数量来检查 map 元素增加的数量。例如：

```
auto pr = people.emplace(Name{"Dan", "Druff"}, 77);
auto count = people.size ();
auto iter = people.emplace_hint (pr.first, Name {"Cal", "Cutta"}, 62);
if(count < people.size ()) std::cout <<"Success!\n";
```

只有当调用 emplace_hint() 后，people 中的元素增加时，才会显示这些信息。