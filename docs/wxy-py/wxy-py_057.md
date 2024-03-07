# Python random 库用法详解

> 原文：[`www.weixueyuan.net/a/637.html`](http://www.weixueyuan.net/a/637.html)

Python 自带了一个 random 库，该库可以随机生成一些数，常用来生成一个随机实数或者随机整数。

我们可以用 random.random() 来随机生成一个 0 到 1 之间的实数。如果希望生成一个整数，则可以使用 random.randint（开始值，结尾值）来得到指定范围内的一个整数。

## 生成随机整数和随机实数

如何知道得到的值是随机分布的呢？可以从两个方面来观察。

第一就是落在任意区间内的数的个数应该是正比于区间的大小的，例如，如果要产生随机实数，那么落在 0 到 0.1 区间内的数应该占 10%，落在 0.1 到 0.6 区间内的数应该占 50%。可以使用下面的代码进行验证。

```

import random
num00_01 = 0
num01_06 = 0
total_num = 10000
while total_num > 0:
    rand_val = random.random()
    if rand_val < 0.1:
        num00_01 = num00_01 + 1
    if 0.6 > rand_val > 0.1:
      num01_06 = num01_06 + 1
    total_num = total_num -1
print(u"落在 0 到 0.1 个个数为%d, 占比为%f" % (num00_01, num00_01/10000.0))
print(u"落在 0.1 到 0.6 个个数为%d, 占比为%f" % (num01_06, num01_06/10000.0))
```

运行结果如下：

$ python rand1.py
落在 0 到 0.1 个个数为 1004, 占比为 0.100400
落在 0.1 到 0.6 个个数为 4950, 占比为 0.495000

如果所有的数都是均匀分布，如要生成 10000 个 1~100 之间的随机整数，如果依次是 100 个 1、100 个 2、...、100 个 100，这也满足前面的条件，但显然不是随机分布的。

随机分布的另一个特点就是各个数出现的平均位置相同。如所有 1 出现的位置之和与所有 10 出现的位置之和应该大致相同，并且平均位置在区间的中间。以上面为例，平均位置应该 是 10000/2=5000。下面使用随机整数来看看生成的随机数是否满足这个条件。

```

import random
val_10_num = 0
val_20_num = 0
val_50_num = 0
total_pos_10 = 0
total_pos_20 = 0
total_pos_50 = 0
total_num = 10000
current_pos = 1
while current_pos <= total_num:
   rand_val = random.randint(1, 100)
   if rand_val == 10:
       val_10_num = val_10_num + 1
       total_pos_10 = total_pos_10 + current_pos       
   if rand_val == 20:
       val_20_num = val_20_num + 1
       total_pos_20 = total_pos_20 + current_pos
   if rand_val == 50:
       val_50_num = val_50_num + 1
   total_pos_50 = total_pos_50 + current_pos
   current_pos = current_pos +1
print(u"10 出现的次数为%d, 平均位置为%d" % (val_10_num, total_pos_10/val_10_num))
print(u"20 出现的次数为%d, 平均位置为%d" % (val_20_num, total_pos_20/val_20_num))
print(u"50 出现的次数为%d, 平均位置为%d" % (val_50_num, total_pos_50/val_50_num))
```

运行结果如下：

$ python rand2.py
10 出现的次数为 97, 平均位置为 4976
20 出现的次数为 104, 平均位置为 5124
50 出现的次数为 98, 平均位置为 5005

可以看到，出现的次数都接近 100 次，平均位置接近 5000。

如果希望实数出现的区间不是 0 到 1，怎么办？可以用平移拉伸的办法。简单来说就是用 y=ax+b，其中 x 是在 0 到 1 之间的随机数，那么 y 就是在区间 b 到 a+b 之间的随机数。下面的例子用来产生 1.5 到 2.9 之间的随机数。

```

import random
start = 1.5
end = 2.9
def generate_random():
    x = random.random()
    y = start + x*(end-start)
    return y
print(generate_random())
print(generate_random())
print(generate_random())
print(generate_random())
print(generate_random())
```

运行结果如下：

$ python rand3.py
2.73776895174
1.99803168825
2.28699864497
2.71552010852
2.88653219406

为了得到指定区间内的随机实数，可以直接使用 random.uniform()，其结果和上例相同。

>>> import random
>>> random.uniform(1.2, 2.9)
2.6148511677324087
>>> random.uniform(1.2, 2.9)
1.3768844360707853
>>> random.uniform(1.2, 2.9)
1.974822517837854
>>> random.uniform(1.2, 2.9)
2.7001362254339325
>>> random.uniform(1.2, 2.9)
2.469935919913799

## 其他形式的随机

另外一个常用的函数是 random.choice()，其接收一个列表，返回值是从输入列表中随机选中一个元素。可以用该函数来模拟掷骰子，如下：

>>> import random              # 引入随机库
>>> dice = [1, 2, 3, 4, 5, 6]  # 允许出现的数为 1~6，不能超出这个范围
>>> random.choice(dice)        # 掷一次骰子
5                       # 骰子的数
>>> random.choice(dice)        # 掷一次骰子
3                       # 骰子的数
>>> random.choice(dice)        # 掷一次骰子
1                       # 骰子的数
>>> random.choice(dice)        # 掷一次骰子
2                       # 骰子的数
>>> random.choice(dice)        # 掷一次骰子
1                       # 骰子的数

另外一个常用函数是 random.shuffle()，用于将某个列表打乱顺序可以使用 random.shuffle() 函数来完成任务。

>>> import random
>>> a = range(10)
>>> a
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> random.shuffle(a)
>>> a
[7, 9, 4, 2, 6, 1, 8, 3, 0, 5]

该函数可以用来模拟洗扑克牌的动作。一副扑克牌有 54 张，最开始它们是有序排列的，下面使用 shuffle() 函数来实现洗牌的功能。

```

import random
def init_cards():
    ret = []
    for i in range(1, 13):
        ret.append((i, u'红桃'))
        ret.append((i, u'黑桃'))
        ret.append((i, u'方片'))
        ret.append((i, u'草花'))
    ret.append((14, u'大王'))
    ret.append((14, u'小王'))
    return ret
cards = init_cards()
random.shuffle(cards)
for card in cards:
    if card[0] == 14:
         print(card[1])
    else:
         print("%s%d" % (card[1], card[0]))
```

运行结果如下：

1$ python setupPoker.py
黑桃 6                     # 随机生成的牌
方片 11
…                         # 数量太大，省略中间部分
红桃 2
草花 8
黑桃 7
小王
方片 3

## 伪随机问题

random 模块生成的数具有基本的随机特性，但其并不是真正的随机数。例如随机生成整数，我们可以让其生成两串整数，每串 10 个数，可以发现这两串数是不同的，并且任意时刻让其生成 10 个整数，它似乎都会生成不同的数。可以用下面的代码来演示这个过程。

```

import random
def generate_20_random_int(start, end):
    ret = []
    for i in range(10):
        ret.append(random.randint(start, end))
    return ret
print(generate_20_random_int(1, 100))
print(generate_20_random_int(1, 100))
print(generate_20_random_int(1, 100))
print(generate_20_random_int(1, 100))
print(generate_20_random_int(1, 100))
```

运行结果如下：

$ python rand4.py
[34, 60, 74, 85, 95, 70, 71, 90, 96, 69]
[51, 45, 82, 14, 17, 67, 7, 21, 94, 51]
[67, 58, 71, 58, 53, 98, 64, 56, 44, 1]
[4, 7, 70, 87, 95, 31, 67, 98, 64, 38]
[94, 86, 45, 4, 84, 12, 32, 74, 43, 26]

但真的是这样吗？其实我们生成的随机序列是受一个叫作种子的变量控制的。只要修改了种子的值，那么生成的序列就是确定的。下面在生成序列之前设定该种子的值，看看得到的序列是否相同。修改后的代码如下：

```

import random
def generate_20_random_int(start, end):
    random.seed(88)                     # 修改了种子的值
    ret = []
    for i in range(10):
        ret.append(random.randint(start, end))
    return ret
print(generate_20_random_int(1, 100))
print(generate_20_random_int(1, 100))
print(generate_20_random_int(1, 100))
print(generate_20_random_int(1, 100))
print(generate_20_random_int(1, 100))
```

运行结果如下：

$ python rand5.py
[40, 19, 91, 63, 53, 70, 86, 69, 14, 100]
[40, 19, 91, 63, 53, 70, 86, 69, 14, 100]
[40, 19, 91, 63, 53, 70, 86, 69, 14, 100]
[40, 19, 91, 63, 53, 70, 86, 69, 14, 100]
[40, 19, 91, 63, 53, 70, 86, 69, 14, 100]

我们发现得到的序列是一样的。这就是为何 random 库生成的是伪随机数，即该模块生成的并不是完全随机数。由于生成完全随机数的难度较大，所以在各种随机数库的实现中生成的都是伪随机数。

> 不过该伪随机数已经可以满足绝大多数需求，一般情况下使用它是不会有什么问题的。