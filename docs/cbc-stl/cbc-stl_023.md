# C++ vector 获取（访问）元素

可以在方括号中使用索引，为现有元素设定值，或者只是通过表达式使用它的值。例如:

```
std::vector<double> values (20);
values[0] = 3.14159;
values[1] = 5.0;
values[2] = 2.0*values[0]*values[1];
```

vector 的索引从 0 幵始，这和标准数组一样。通过使用索引，总是可以访问到现有的元素，但是不能这样生成新元素——需要使用 push_back()、insert()、emplace() 或 emplace_back()。当像这样索引一个 vector 时，和数组容器一样，并没有检查索引值，所以当索引可能越界时，应该通过 at() 函数去使用这个元素。

vector 的成员函数 front() 和 back() 分別返回序列中第一个和最后一个元素的引用，例如：

```
std::cout << values.front () << std::endl; // Outputs 3.14159
```

因为成员函数 front() 和 back() 返回的是引用，所以它们可以出现在赋值运算符的左边。

```
values.front() = 2.71828;
```

成员函数 data() 返回一个指向数组的指针，它在内部被用来存储元素。例如：

```
auto pData = values.data();
```

pData 是 double* 类型，一般来说，data() 返回 vector<T> 容器的 T* 类型的值。必要时可以使用这个函数。