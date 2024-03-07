# C++随机数生成器（default_random_engine）用法详解

默认随机数生成器是 std::default_random_engine 类型别名定义的随机无符号整数的通用源。这个别名表示实现是被定义的，选择的模板类型参数需要能够为用户提供他们满意的序列。下面是一种生成 default_random_engine 类型的迭代器的简单方式：

```
std::default_random_engine rngl; // Create random number generator with default seed
```

这里调用默认的构造函数，因此会用默认种子来设置初始状态。在不同的时刻执行从 rng1 产生的随机数字序列总是相同的，因为种子保持不变。当然，也能自己提供种子：

```
std::default_random_engine rng2 {10}; // Create rng with 10 as seed
```

这里以 10 为种子生成了 rng2，因此从这个生成器中得到的随机序列和从 rng1 得到的是不同的，但仍然是固定的。如果想在每次执行代码时都得到不同的序列，需要提供非确定性的种子：

```
std::random_device rd; // Non-determinstic seed source
std::default_random_engine rng3 {rd()}; // Create random number generator
```

种子值是通过 random_device 类型的函数对象 rd 获得的。每一个 rd() 调用都会返回不同的值，而且如果我们实现的 random_devic 是非确定性的，程序每次执行连续调用 rd() 都会产生不同的序列。

另一个选择是，提供一个 seed_seq 对象作为 default_random_engine 构造函数的参数：

```
std::seed_seq sd_seq {2, 4, 6, 8};
std::default_random_engine rng4 {sd_seq}; // Same random number sequence each time
std::random_device rd;  // Non-determinstic seed source
std::default_random_engine rng5 {std::seed_seq{rd(), rd(), rd()}};
```

这里第一次用从固定的初始种子生成的 seed_seq 对象生成 rng4 生成器；每次执行这段代码，从 rng4 得到的序列都是相同的。 rng5 是从 seed_seq 构造的，seed_seq 拥有 random_device 函数对象产生的值，因此我们永远不知道会产生什么序列（每次都是一个惊喜）。我们现在正处于一个创造和使用分布对象的位置，并开始一些严肃的随机活动。

## 创建分布对象

正如之前所说的，为了产生分布内的值，表示分布的函数对象需要一个随机数生成器对象作为参数。每次执行分布对象，都会返回一个在它所表示的分布之内的随机数，这个随机数是从随机数生成器所获得的值生成的。在分布返回的第一个值后面的连续值都依赖前面的值。创建一个分布对象需要一系列依赖分布类型的参数值。例如，均匀分布需要生成值的上下边界，而正态分布需要的是平均值和标准差。尽管不同类型的分布之间有本质上的不同，但它们还是有很多共同之处。所有的分布对象都有下面这些公共成员：

*   result_type 是在类中为生成值的类型定义的类型别名。
*   min() 是一个返回分布对象可生成的最小值的成员函数。
*   max() 是一个返回分布对象可生成的最大值的成员函数。
*   reset() 是一个可以将分布对象重置为它的原始状态的成员函数，这样下次返回的值就不会依赖前一个值。这是否会发生取决于我们的实现。分布返回的值是独立的，reset() 不会做任何事情。
*   param_type 是定义在类中的一个结构体的类型别名。不同的分布需要不同的参数值，可能还需要是不同的类型，这些值会被保存到一个用来指定分布的 param_type 类结构体中。
*   param() 是一个接受一个 param_type 类型的参数的成员函数，它会将分布对象的参数重置为新的值。
*   param() 的无参重载版本，它会返回分布对象所包含的 param_type 对象。
*   默认构造函数对于定义分布的参数有默认值。
*   一个构造函数，接受 param_type 类型的参数来定义分布。

后面会展示这些成员函数如何用于某些类型的分布。为了可以将分布对象的内部状态传送到流，或为了从流中读回状态，对流的插入和提取运算符、< 和 >>，为分布类型进行了重载。这样就提供了恢复程序的上一次执行中分布的状态的能力。