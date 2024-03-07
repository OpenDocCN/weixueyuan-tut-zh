# C++ generate_canonical 均匀分布随机数函数用法详解

标准均匀分布是一个在范围 [0，1) 内的连续分布。generate_canonical() 函数模板会提供一个浮点值范围在 [0，1) 内，且有给定的随机比特数的标准均匀分布。它有 3 个模板参数：浮点类型、尾数的随机比特的个数，以及使用的随机数生成器的类型。函数的参数是一个随机数生成器，因此最后一个模板参数可以推导出来。下面是它可能的用法：

```
std::vector<double> data(8); // Container with 8 elements
std::random_device rd; // Non-determinstic seed source
std::default_random_engine rng {rd()}; // Create random number generator
std::generate(std::begin(data), std::end(data),[&rng] { return std::generate_canonical<double, 12> (rng); });
std::copy(std::begin(data),std::end(data),std::ostream_iterator<double> {std::cout, " "});
```

在 lambda 表达式中，被调用的 generate_canonical() 函数被用来作为 generate() 算法的第三个参数。lambda 表达式会返回一个有 12 个随机比特的 double 类型的随机值，因此 generate() 会用这种数据来填充 data 中的元素。执行这些语句会产生如下输出：

0.766197 0.298056 0.409951 0.955263 0.419199 0.737496 0.547764 0.91622

上面的输出说明位数可能我们想要的要多，记住我们只指定了 12 个随机比特。可以按如下方式限制输出：

```
std::copy(std::begin(data), std::end(data),std::ostream_iterator<double>{std::cout << std::fixed<< std::setprecision (4) , " "});
```

流操作符被应用到每个输出值，因此现在的输出可能如下所示：

0.8514 0.5707 0.8322 0.6626 0.7026 0.8854 0.5427 0.8886

如果真的想得到这些位，可以用 hexfloat 以十六进制的格式输出这些值。

显然，随机位数越少，可能的随机值的范围越有限。可以通过将位数指定为这个类型的最大值来使范围达到最大。下面是展示如何这么做的代码：

```
std::vector<long double> data; // Empty container
std::random device rd; // Non-determinstic seed source
std::default_random_engine rng {rd()};//Create random number generator
std::generate_n(std::back_inserter(data), 10, [&rng]{return std::generate_canonical<long double,std::numeric_limits<long double>::digits>(rng); });
std::copy(std::begin(data), std::end(data),std::ostream_iterator<long double> {std:: cout, " "});
std::cout << std::endl;
```

注意，这和前面的代码有些区别。这次，generate_n() 的第一个参数是 data 容器的 back_insert_iterator，因此可以通过调用它的成员函数 push_back() 来添加元素。generate_canonical() 的第二个模板参数是 numeric_limits 对象的 long double 类型的成员变量的 digits 值。这是这个类型的位数的比特数，因此可以指定这个类型可能的随机比特位的最大个数(在笔者系统上只有 53 个)。笔者得到了这样的输出，但你的可能是不同的：

0.426365 0.0635646 0.208444 0.198286 0.338378 0.490884 0.841733 0.975676 0.193322 0.346017