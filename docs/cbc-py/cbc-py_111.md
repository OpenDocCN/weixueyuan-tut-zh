# Python random 模块及用法

random 模块主要包含生成伪随机数的各种功能变量和函数。

在 Python 的交互式解释器中先导入 random 模块，然后输入 random.__all__ 命令（__all__ 变量代表了该模块开放的公开接口），即可看到该模块所包含的全部属性和函数：

>>> random.__all__
['Random', 'seed', 'random', 'uniform', 'randint', 'choice', 'sample', 'randrange', 'shuffle', 'normalvariate', 'lognormvariate', 'expovariate', 'vonmisesvariate', 'gammavariate', 'triangular', 'gauss', 'betavariate', 'paretovariate', 'weibullvariate', 'getstate', 'setstate', 'getrandbits', 'choices', 'SystemRandom']

开发者同样不需要完全记住这些属性和函数的含义，在使用时可参考[`docs.python.org/3/library/random.html`](https://docs.python.org/3/library/random.html)。

在 random 模块下提供了如下常用函数：

*   random.seed(a=None, version=2)：指定种子来初始化伪随机数生成器。
*   random.randrange(start, stop[, stop])：返回从 start 开始到 stop 结束、步长为 step 的随机数。其实就相当于 choice(range(start, stop, step)) 的效果，只不过实际底层并不生成区间对象。
*   random.randint(a, b)：生成一个范围为 a≤N≤b 的随机数。其等同于 randrange(a, b+1) 的效果。
*   random.choice(seq)：从 seq 中随机抽取一个元素，如果 seq 为空，则引发 IndexError 异常。
*   random.choices(seq, weights=None, cum_weights=None, k=1)：从 seq 序列中抽取 k 个元素，还可通过 weights 指定各元素被抽取的权重（代表被抽取的可能性高低）。
*   random.shuffle(x[, random])：对 x 序列执行洗牌“随机排列”操作。
*   random.sample(population, k)：从 population 序列中随机抽取 k 个独立的元素。
*   random.random()：生成一个从 0.0（包含）到 1.0（不包含）之间的伪随机浮点数。
*   random.uniform(a, b)：生成一个范围为 a≤N≤b 的随机数。
*   random.expovariate(lambd)：生成呈指数分布的随机数。其中 lambd 参数(其实应该是 lambda，只是 lambda 是 Python 关键字，所以简写成 lambd）为 1 除以期望平均值。如果 lambd 是正值，则返回的随机数是从 0 到正无穷大；如果 lambd 为负值，则返回的随机数是从负无穷大到 0。

关于生成伪随机浮点数的函数，Python 还提供了 random.triangular(low, high, mode)、random.gauss(mu, sigma) 等，它们用于生成呈对称分布、高斯分布的随机数。由于这种随机数需要一些数学知识来理解它们，而且平时开发并不常用，因此这里就不深入展开介绍了。

下面程序示范了 random 模块中常见函数的功能和用法：

```
import random
#生成范围为 0.0≤x<1.0 的伪随机浮点数
print (random.random())
#生成范围为 2.5≤x<10.0 的伪随机浮点数
print (random.uniform(2.5, 10.0))
#生成呈指数分布的伪随机浮点数
print (random.expovariate(1/5))
#生成从 0 到 9 的伪随机整数
print(random.randrange(10))
#生成从 0 到 100 的随机偶数
print (random.randrange(0, 101 , 2))
#随机抽取一个元素
print (random.choice (['Python','Swift','Kotlin']))
book_list = ['Python','Swift','Kotlin']
#对列表元素进行随机排列
random.shuffle (book_list)
print (book_list)
#随机抽取 4 个独立的元素
print (random.sample([10, 20 , 30 , 40 , 50], k=4))
```

运行上面程序，可以看到如下输出结果：

0.7097266660597923
6.4845555071809535
9.707899380000603
2
70
Python
['Swift', 'Python', 'Kotlin']
[20, 10, 30, 50]

实际上，使用 random 模块中的随机函数可以做很多很有趣的事情。比如下面程序：

```
import random
import collections
#指定随机抽取 6 个元素，各元素被抽取的权重（概率）不同
print(random.choices(['Python','Swift','Kotlin'], [5, 5, 1], k=6))
#下面模拟从 52 张扑克牌中抽取 20 张
#在被拍到的 20 张牌中，牌面为 10（包括 J、Q、K 的的牌占多大比例
#生成一个 16 个 tens（代表 10）和 36 个 low_cards（代表其他牌）的集合
deck= collections.Counter(tens=16, low_cards=36)
#从 52 张牌中随机抽取 20 张
seen= random.sample(list(deck.elements()), k=20)
#统计 tens 元素有多少个，再除以 20
print(seen.count('tens')/20)
```

运行上面程序，可以看到如下输出结果：

['Swift', 'Swift', 'Swift', 'Swift', 'Python', 'Swift']
0.45

从上面的输出结果来看，在第一次抽取的 6 个元素中 Kotlin 完全没有被抽取到，这是因为它的被抽取比例太低了。