# Python itertools 模块：生成迭代器（实例分析）

itertools 模块中主要包含了一些用于生成迭代器的函数。在 Python 的交互式解释器中先导入 itertools 模块，然后输入 `[e for e in dir(itertools) if not e.startswith('_')]` 命令，即可看到该模块所包含的全部属性和函数：

>>> [e for e in dir(itertools) if not e.startswith('_')]
['accumulate', 'chain', 'combinations', 'combinations_with_replacement', 'compress', 'count', 'cycle', 'dropwhile', 'filterfalse', 'groupby', 'islice', 'permutations', 'product', 'repeat', 'starmap', 'takewhile', 'tee', 'zip_longest']

从上面的输出结果可以看出，itertools 模块中的不少函数都可以用于生成迭代器。

先看 itertools 模块中三个生成无限迭代器的函数：

1.  count(start, [step])：生成 start, start+step, start+2*step，... 的迭代器，其中 step 默认为 1。比如使用 count(10) 生成的迭代器包含：10, 11 , 12 , 13, 14，...。
2.  cycle(p)：对序列 p 生成无限循环 p[0], p[1],..., p[0], p[1],... 的迭代器。比如使用 cycle('ABCD') 生成的迭代器包含：A,B,C,D,A,B,C,D,...。
3.  repeat(elem [,n])：生成无限个 elem 元素重复的迭代器，如果指定了参数 n，则只生成 n 个 elem 元素。比如使用 repeat(10, 3) 生成的法代器包含：10, 10, 10。

下面程序示范了使用上面三个函数来生成迭代器：

```
import itertools as it
# count(10, 3)生成 10、13、16……迭代器
for e in it.count(10, 3):
    print(e)
    # 用于跳出无限循环
    if e > 20:
        break
print('---------')
my_counter = 0
# cycle 用于对序列生成无限循环的迭代器
for e in it.cycle(['Python', 'Kotlin', 'Swift']):
    print(e)
    # 用于跳出无限循环
    my_counter += 1
    if my_counter > 7:
        break
print('---------')
# repeat 用于生成 n 个元素重复的迭代器
for e in it.repeat('Python', 3):
    print(e)
```

在 itertools 模块中还有一些常用的迭代器函数，如下所示：

*   accumulate(p[,func])：默认生成根据序列 p 元素累加的迭代器，p[0], p[0]+p[1], p[0]+p[1]+p[2]，...序列，如果指定了 func 函数，则用 func 函数来计算下一个元素的值。
*   chain(p, q, ...)：将多个序列里的元素“链”在一起生成新的序列。
*   compress(data, selectors)：根据 selectors 序列的值对 data 序列的元素进行过滤。如果 selector[0] 为真，则保留 data[0]；如果 selector[1] 为真，则保留 data[1]......依此类推。
*   dropwhile(pred, seq)：使用 pred 函数对 seq 序列进行过滤，从 seq 中第一个使用 pred 函数计算为 False 的元素开始，保留从该元素到序列结束的全部元素。
*   takewhile(pred, seq)：该函数和上一个函数恰好相反。使用 pred 函数对 seq 序列进行过滤，从 seq 中第一个使用 pred 函数计算为 False 的元素开始，去掉从该元素到序列结束的全部元素。
*   filterfalse(pred, seq)：使用 pred 函数对 seq 序列进行过滤，保留 seq 中使用 pred 计算为 True 的元素。比如 `filterfalse(lambda x:x%2, range(10))`，得到 0, 2, 4, 6, 8。
*   islice(seq, [start,] stop [, step])：其功能类似于序列的 slice 方法，实际上就是返回 `seq[start:stop:step]` 的结果。
*   starmap(func, seq)：使用 func 对 seq 序列的每个元素进行计算，将计算结果作为新的序列元素。当使用 func 计算序列元素时，支持序列解包。比如 seq 序列的元素长度为 3，那么 func 可以是一个接收三个参数的函数，该函数将会根据这三个参数来计算新序列的元素。
*   zip_longest(p,q,...)：将 p、q 等序列中的元素按索引合并成元组，这些元组将作为新序列的元素。

上面这些函数的测试程序如下：

```
import itertools as it
# 默认使用累加的方式计算下一个元素的值
for e in it.accumulate(range(6)):
    print(e, end=', ') # 0, 1, 3, 6, 10, 15
print('\n---------')
# 使用 x*y 的方式来计算迭代器下一个元素的值
for e in it.accumulate(range(1, 6), lambda x, y: x * y):
    print(e, end=', ') # 1, 2, 6, 24, 120
print('\n---------')
# 将两个序列“链”在一起，生成新的迭代器
for e in it.chain(['a', 'b'], ['Kotlin', 'Swift']):
    print(e, end=', ') # 'a', 'b', 'Kotlin', 'Swift'
print('\n---------')
# 根据第二个序列来筛选第一个序列的元素，
# 由于第二个序列只有中间两个元素为 1（True），因此前一个序列只保留中间两个元素
for e in it.compress(['a', 'b', 'Kotlin', 'Swift'], [0, 1, 1, 0]):
    print(e, end=', ') # 只有: 'b', 'Kotlin'
print('\n---------')
# 获取序列中从长度不小于 4 的元素开始、到结束的所有元素
for e in it.dropwhile(lambda x:len(x)<4, ['a', 'b', 'Kotlin', 'x', 'y']):
    print(e, end=', ') # 只有: 'Kotlin', 'x', 'y'
print('\n---------')
# 去掉序列中从长度不小于 4 的元素开始、到结束的所有元素
for e in it.takewhile(lambda x:len(x)<4, ['a', 'b', 'Kotlin', 'x', 'y']):
    print(e, end=', ')  # 只有: 'a', 'b'
print('\n---------')
# 只保留序列中从长度不小于 4 的元素
for e in it.filterfalse(lambda x:len(x)<4, ['a', 'b', 'Kotlin', 'x', 'y']):
    print(e, end=', ') # 只有: 'Kotlin'
print('\n---------')
# 使用 pow 函数对原序列的元素进行计算，将计算结果作为新序列的元素
for e in it.starmap(pow, [(2,5), (3,2), (10,3)]):
    print(e, end=', ') # 32, 9, 1000
print('\n---------')
# 将'ABCD'、'xy'的元素按索引合并成元组，这些元组作为新序列的元素
# 长度不够的序列元素使用'-'字符代替
for e in it.zip_longest('ABCD', 'xy', fillvalue='-'):
    print(e, end=', ') # ('A', 'x'), ('B', 'y'), ('C', '-'), ('D', '-')
```

运行上面程序，可以看到如下输出结果：

0, 1, 3, 6, 10, 15,
---------
1, 2, 6, 24, 120,
---------
a, b, Kotlin, Swift,
---------
b, Kotlin,
---------
Kotlin, x, y,
---------
a, b,
---------
Kotlin,
---------
32, 9, 1000,
---------
('A', 'x'), ('B', 'y'), ('C', '-'), ('D', '-'),

在 itertools 模块中还有一些用于生成排列组合的工具函数：

*   product(p, q, ...[repeat= 1)]：用序列 p 、q 、... 中的元素进行排列组合，就相当于使用嵌套循环组合。
*   permutations(p[, r])：从序列 p 中取出 r 个元素组成全排列，将排列得到的元组作为新迭代器的元素。
*   combinations(p, r)：从序列 p 中取出 r 个元素组成全组合，元素不允许重复，将组合得到的元组作为新迭代器的元素。
*   combinations with_replacement(p, r)，从序列 p 中取出 r 个元素组成全组合，元素允许重复，将组合得到的元组作为新迭代器的元素。

如下程序示范了上面 4 个函数的用法：

```
import itertools as it
# 使用两个序列进行排列组合
for e in it.product('AB', 'CD'):
    print(''.join(e), end=', ') # AC, AD, BC, BD,
print('\n---------')
# 使用一个序列、重复 2 次进行全排列
for e in it.product('AB', repeat=2):
    print(''.join(e), end=', ') # AA, AB, BA, BB,
print('\n---------')
# 从序列中取 2 个元素进行排列
for e in it.permutations('ABCD', 2):
    print(''.join(e), end=', ') # AB, AC, AD, BA, BC, BD, CA, CB, CD, DA, DB, DC,
print('\n---------')
# 从序列中取 2 个元素进行组合、元素不允许重复
for e in it.combinations('ABCD', 2):
    print(''.join(e), end=', ') # AB, AC, AD, BC, BD, CD,
print('\n---------')
# 从序列中取 2 个元素进行组合、元素允许重复
for e in it.combinations_with_replacement('ABCD', 2):
    print(''.join(e), end=', ') # AA, AB, AC, AD, BB, BC, BD, CC, CD, DD,
```

上面程序用到了一个字符串的 join() 方法，该方法用于将元组的所有元素连接成一个字符串。运行程序，可以看到如下输出结果：

AC, AD, BC, BD,
---------
AA, AB, BA, BB,
---------
AB, AC, AD, BA, BC, BD, CA, CB, CD, DA, DB, DC,
---------
AB, AC, AD, BC, BD, CD,
---------
AA, AB, AC, AD, BB, BC, BD, CC, CD, DD,