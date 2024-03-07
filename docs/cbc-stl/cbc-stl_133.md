# C++ discrete_distribution 离散分布随机数函数用法详解

discrete_distribution 模板定义了返回随机整数的范围在 [0，n) 内的分布，基于每个从 0 到 n-1 的可能值的概率权重。权重可以使我们能够决定为返回值使用何种分布。这种分布通常用返回值来选择随机对象，或从可以用索引访问的序列得到的值。序列可以包含任何类型的对象，包括函数对象，因此提供了极大的灵活性。如果想实现一个水果机模拟器，这种分布会有帮助。

必须为生成的值提供一些权重；权重的数量会决定生成的可能值的数目，而且权重的值也被用来决定概率。下面是一个示例，演示了该如何模拟投掷一个面值从 1 到 6 的骰子：

```
std::discrete_distribution<size_t> d{1, 1, 1, 1, 1, 3}/ // Six possible values std::random_device rd;
std::default_random_engine rng {rd()};
std::map<size_t, size_t> results;   // Store the results of throws
for(size_t go {}; go < 5000; ++go)  // 5000 throws of the die
++results[d(rng)];
for(const auto& pr : results)
    std::cout << "A" << (pr.first+1) << " was thrown " << pr.second << " times\n";
```

构造函数的初始化列表包含 6 个权值，因此分布只会生成 [0，6) 这个范围内的值，这意味着只包含从 1 到 5 的值。最后一个权值是其他权值的 3 倍，因此它出现的可能性是其他权值的 3 倍。执行这段代码会生成如下内容：

A 1 was thrown 607 times
A 2 was thrown 645 times
A 3 was thrown 637 times
A 4 was thrown 635 times
A 5 was thrown 617 times
A 6 was thrown 1859 times

6 出现的可能性更大。权重是用来表示生成的整数的相对概率的浮点值。每个值的概率是它的权重除以所有权重之和，所以前 5 个值中的每一个的概率都是 1/8，最后一个值的概率是 3/8。下面这条语句也会产生同样的分布：

```
std::discrete_distribution<size_t> d{20, 20, 20, 20, 20, 60};
```

从 0 到 4，每个值的概率为 20/160，也就是 1/8，最后一个值的概率是 60/160 或 3/8。 也可以用序列指定权重。下面是使用相同的随机数生成器的第一个代码段的变化版：

```
std::array<double,6> wts {10.0, 10.0, 10.0, 10.0, 10.0, 30.0};
std::discrete_distribution<size_t> d{std::begin(wts), std::end(wts)};
std::array<string, 6> die_value {"one", "two", "three", "four", "five", "six"};
std::map<size_t, size_t> results;  // Store the results of throws
for(size_t go {}; go < 5000; ++go)  // 5000 throws of the die
    ++results[d(rng)];
for(const auto& pr : results)
    std::cout << " A " << die_value [pr.first] << " was thrown " << pr.second << " times\n";
```

这里的权值是从数组容器中得到的。分布对象生成的值被用来对数组进行索引输出。下面是得到的输出：

A one was thrown 653 times
A two was thrown 601 times
A three was thrown 611 times
A four was thrown 670 times
A five was thrown 600 times
A six was thrown 1865 times

更进一步，可以选择为 discrete_distribution 对象定义权重来提供一个具有一元函数的构造函数，此构造函数可以从两个参数值中生成给定个数的权重。这种工作方式有一些复杂，因此我们会一步一步地检查它。

这个构造函数有 4 个参数：

*   权重 n 的个数两个；
*   double 类型的值：xmin 和 xmax，通常被用来计算概率；
*   一元运算符 op；

xmax 必须大于 xmin，如果 n 是 0，只有值为 1 的概率才会生成。因此在这种情况下，分布总会产生相同的值 0。

增量会被定义为 (xmax - xmin)/n，又称步进。可以通过执行表达式 op(xmin + (2*k+1)* step/2) 来计算 k 从 0 到 n-1 的概率。

因此权重为：

```
op(xmin + step/2), op(xmin + 3*step/2), op(xmin + 5*step/2),... op(xmin + (2*n-1)*step/2)
```

数值的示例可以帮助说明发生了什么。假设 n 是 6、xmin 是 0、xmax 是 12，因此步进值为 2。如果我们假设定义了使参数翻倍的 op，权重为 2、6、10、14、18、22，概率因此为 1/36、1/12、5/36、7/36、1/4、11/36。下面是这个分布对象的定义：

```
std::discrete_distribution<size_t> dist {6, 0, 12, [](double v) { return 2*v; }};
```

一元运算符是由 lambda 表达式定义的，它会返回参数值的两倍。可以通过调用 discrete_distribution 对象的成员函数 probabilities() 来获取概率。对于 dist 对象可以按如下方式获取概率：

```
auto probs = dist.probabilities(); // Returns type vector<double>
std::copy(std::begin(probs), std::end(probs),std::ostream_iterator<double> { std::cout << std::fixed << std:: setprecision (2), " "});
std::cout << std::endl; // Output: 0.03 0.08 0.14 0.19 0.25 0.31
```

通常，概率的个数是任意的，它和指定的权重的个数对应，因此这里返回了一个 vector<double> 容器。注释中显示的输出对应于先前展示的分数值。

可以调用成员函数 param()，为有不同权重值的 discrete_distribution 对象设置新的概率；权重的个数也可以是不同的：

```
dist.param({2, 2, 2, 3, 3});    // New set of weights
auto parm = dist.param().probabilities(); std::copy(std::begin(parm), std::end(parm),std::ostream_iterator<double> {std::cout << std::fixed << std::setprecision (2)," "});
std::cout << std::endl;  // Output: 0.17 0.17 0.17 0.25 0.25
```

第一次调用 pamm() 成员函数时，它的参数是一个权重列表，这个列表中的值和原始值不同，值的个数超过之前的。调用无参数的 param() 版本会返回一个 param_type 对象，但是并不能准确地知道别名代表的类型是什么。然而，我们知道它提供了和原始的分布对象相同的成员函数来访问参数。在这个示例中，意味着可以通过调用 param_type 对象的成员函数 probabilities() 来得到 param_type 对象中的值。这会返回一个 vector<double> 容器，然后就可以访问它。注释显示了它所包含的概率，并且可以看出它们是和新的权值对应的。