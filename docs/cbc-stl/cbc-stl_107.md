# C++ prev_permutation(STL prev_permutation)算法详解

next_permutation() 是按照字典升序的方式生成的排列。当我们想以降序的方式生成排列时，可以使用 prev_permutation()。

prev_permutation 和 next_permutation() 一样有两个版本，默认使用 < 来比较元素。因为排列是以降序的方式生成的，所以算法大多数时候会返回 true。当生成最大排列时，返回 false。例如：

```
std::vector<double> data {44.5, 22.0, 15.6, 1.5};
do {
    std::copy(std::begin(data), std::end(data), std::ostream_iterator<double> {std::cout, " "});
    std::cout << std::endl;
} while(std::prev_permutation(std::begin(data), std::end(data)));
```

这段代码会输出 data 中 4 个 double 值的全部 24 种排列，因为初始序列是最大排列，所以 prev_permutation() 会在输入最小排列时，才返回 false。