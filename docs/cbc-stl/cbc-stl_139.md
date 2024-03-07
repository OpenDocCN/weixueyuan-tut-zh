# C++梅森旋转算法生成随机数（mersenne_twister_engine）详解

mersenne_twister_engine 类模板实现了梅森选择算法，它被这样叫是因为周期长度是一个梅森素数。

梅森素数是 2n-1 形式的素数，因此 7 和 127 是梅森素数；当然，用在这个算法中的梅森素数更大。这个引擎的应用非常广泛，因为它可以生成非常高质量的序列，但存在速度相对较慢的缺点。这个算法很复杂并且包含很多的参数，因此在此不作解释。

## 梅森素数算法实例

对于定义具体生成器的 mersenne_twister_engine 的实例有两个类型别名。mtl9937 生成随机的无符号 32 位整数，mtl9937_64 生成无符号的 64 位整数。mtl9937 随机数生成器的周期长度为 219937-1，它因此得名。

可以用和其他生成相同的方式使用它们：

```
std::random_device rd;
std::mtl9937_64 rng {rd()}; // Generates random 64-bit integers
std::uniform_real_distribution<long double> dist {-5.OL, 5.OL};
for(size_t i {}; i < 8; ++i)
    std::cout << std::setw(5)<< dist(rng)<<" "; //-2.57481 3.0546 -1.6438 2.14798 -3.84095 0.973843 -2.98971 -2.1067
```