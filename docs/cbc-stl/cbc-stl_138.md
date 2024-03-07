# C++线性同余法生成随机数（linear_congruential_engine）用法详解

linear_congruential_engine 类模板实现了一个最老且最简单的生成整数随机序列的算法，它被叫作线性同余法。这个算法包含 3 个参数：乘数 a、增量 c 和模 m。这些值的选择对于生成合理质量的随机序列至关重要。这个过程需要单个的整数种子和第一个随机值 x，x 理论上可以像这样计算：

unsigned int x = (a*seed + c) % m;

每个随机数 xn 都可以用下面这个等式生成下一个 xn+l ：

xn+1=(axn+c) modm

显然，因为随机值是余数，可以生成的不同值的最大个数是 m，并且 a 和 c 的选择不多，它所生成的值的个数会比 m 更少。然而这个算法是简单快速的，在髙质量的随机序列对程序很重要时，最好选择其他引擎实例的生成器，例如 mersenne_twister_engine。

## 基于线性同余的生成器

有两个被定义为 linear_congmential_engine 模板实例别名的随机数生成器类型：minstd_rand() 和生成 32 位无符号整数的 minstd_rand。名字来自于 “minimum standard random number generator” minstd_rand() 是 1998 年由 Stephen K.Park 和 Keith W.Miller 为生成随机数而提出的最低标准，因为那时候的生成器很少。a 被定义为 16 807，c 是 0，m 是 2 147 483 647。 m 的值是小于 232 的最大梅森素数。minstd_rand 生成器是 a 为 48271 的 minstd_rand() 的一个改进版本。

归因于 Donald Knuth, knuth_b 随机数生成器实现了一个可以将 shuffle_order_engine 适配器应用到 minst_rand() 生成器所产生的值上的算法。这被描述在他的经典著作《计算机程序设计的艺术：卷 2》中，并伴随着大量的随机数生成方法和随机性测试。通过移除连续值之间的依赖来运用适配器增加序列的“随机性”。

这些生成器（事实上所有的生成器）的使用方式都和前面看到的 default_random_ engine 相同，例如：

```
std::random_device rd;
std::minstd_rand rng {rd()};
std::uniform_int_distribution<long> dist {-5L, 5L};
for(size_t i{}; i < 8; ++i)
std::cout << std::setw (2) << dist (rng) <<" "; // 3 -5 -2 4 -5 4 1 0
```