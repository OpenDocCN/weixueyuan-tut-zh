# C++ array 元素的比较

可以用任何比较运算符比较两个数组容器，只要它们有相同的大小，保存的是相同类型的元素，而且这种类型的元素还要支持比较运算符。示例如下：

```
std::array<double,4> these {1.0, 2.0, 3.0, 4.0};
std::array<double,4> those {1.0, 2.0, 3.0, 4.0};
std::array<double,4> them {1.0, 3.0, 3.0, 2.0};

if (these == those) std::cout << "these and those are equal." << std::endl;
if (those != them) std::cout << "those and them are not equal."<< std::endl;
if (those < them) std::cout << "those are less than them."<< std::endl;
if (them > those) std::cout << "them are greater than those." << std::endl;
```

容器被逐元素地比较。对 ==,如果两个数组对应的元素都相等，会返回 true。对于 !=，两个数组中只要有一个元素不相等，就会返回 true。这也是字典中单词排序的根本方式，两个单词中相关联字母的不同决定了单词的顺序。这个代码片段中所有的比较运算都是 true。所以当它们执行时，会输出 4 条消息。

不像标准数组，只要它们存放的是相同类型、相同个数的元素，就可以将一个数组容器赋给另一个。例如：

```
them = those;
```

赋值运算符左边的数组容器的元素，将会被赋值运算符心边的数组容器的元素覆盖。