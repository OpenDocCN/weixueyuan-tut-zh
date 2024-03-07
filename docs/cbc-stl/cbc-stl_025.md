# C++ vector 添加（增加）元素

记住，向容器中添加元素的唯一方式是使用它的成员函数。如果不调用成员函数，非成员函数既不能添加也不能删除元素。这意味着容器对象必须通过它所允许的函数去访问，迭代器显然不行。

可以通过使用容器对象的 push_back() 函数，在序列的末尾添加一个元素。例如：

```
std::vector<double> values;
values.push_back(3.1415926);
```

在这个示例中，push_back() 函数以传入的参数 -3.1415926 作为新元素的值，然后把它添加到现有元素的后面。因为这里并没有现有的元素，所以这个元素就是第一个元素。如果没有调用 reserve()，容器就会为这个新元素分配内存。这里，第二个版本的 push_back() 使用了右值引用参数，这样就可以通过移动运算来添加元素。例如：

```
std::vector<std::string> words;
words.push_back(string("adiabatic"));
// Move string("adiabatic"） into the vector
```

这里 push_back() 的参数是一个临时对象，因此这会调用右值引用版的函数。当然，也可以这样写：

```
words.push_back ("adiabaticft"); // Move string("adiabatic"） into the vector
```

编译器会生成一个以"adiabatic”为初值的 string 对象，然后这个对象会像前面那样移动到 vector 中。 

还有一个更好的方法来添加元素。emplace back() 比 push_back() 更有效率。下面这个代码片段说明了为什么：

```
std::vector<std::string> words;
words.push_back (std:: string ("facetious") ) ; // Calls string constructor & moves the string object 
words•emplace_back("abstemious"）；// Calls string constructor to create element in place
```

emplace_back() 的参数正是添加到容器中的对象的构造函数所需要的参数。emplace_back() 用它的参数作为构造函数的参数，在容器中生成对象。如果不想使用移动运算，这个示例中就要使用 push_back()。可以在 emplace_back() 函数中使用尽可能多的参数，只要它们满足对象构造函数的要求。这里有一个使用多参数的 emplace_back() 的示例：

```
std::string str {"alleged"};
words.emplace_back(str, 2, 3);
// Create string object corresponding to "leg" in place
```

emplace_back() 函数会调用接收三个参数的 string 构造函数，生成 string 对象，然后把它添加到 words 序列中。构造函数会生成一个从索引 2 幵始、包含 str 中三个字符的子串。