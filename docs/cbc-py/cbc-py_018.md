# Python 字符串操作（查找，替换，分割和连接）方法及其使用

str 提供了如下常用的执行查找、替换等操作的方法：

*   startswith()：判断字符串是否以指定子串开头。
*   endswith()：判断字符串是否以指定子串结尾。
*   find()：查找指定子串在字符串中出现的位置，如果没有找到指定子串，则返回 -1。
*   index()：查找指定子串在字符串中出现的位置，如果没有找到指定子串，则引发 ValueError 错误。
*   replace()：使用指定子串替换字符串中的目标子串。
*   translate()：使用指定的翻译映射表对字符串执行替换。

如下代码示范了上面方法的用法：

```
s = 'crazyit.org is a good site'
# 判断 s 是否以 crazyit 开头
print(s.startswith('crazyit'))
# 判断 s 是否以 site 结尾
print(s.endswith('site'))
# 查找 s 中'org'的出现位置
print(s.find('org')) # 8
# 查找 s 中'org'的出现位置
print(s.index('org')) # 8
# 从索引为 9 处开始查找'org'的出现位置
#print(s.find('org', 9)) # -1
# 从索引为 9 处开始查找'org'的出现位置
print(s.index('org', 9)) # 引发错误
# 将字符串中所有 it 替换成 xxxx
print(s.replace('it', 'xxxx'))
# 将字符串中 1 个 it 替换成 xxxx
print(s.replace('it', 'xxxx', 1))
# 定义替换表：97（a）->945（α）,98（b）->945（β）,116（t）->964（τ）,
table = {97: 945, 98: 946, 116: 964}
print(s.translate(table)) # crαzyit.org is α good site
```

上面代码中，查找‘org’在 s 字符串中出现的位置，但由于第二个参数指定从索引 9 处开始查找，这样在该字符串中无法找到‘org’，因此这行代码将会引发 ValueError 错误。

从上面程序可以看出，str 的 translate() 方法需要根据翻译映射表对字符串进行查找、替换。在上面程序中我们自己定义了一个翻译映射表，这种方式需要开发者能记住所有字符的编码，这显然不太可能。为此，Python 为由类提供了一个 maketrans() 方法，通过该方法可以非常方便地创建翻译映射表。

假如定义 a->α、b->β、t->τ 的映射，程序只要将需要映射的所有字符作为 maketrans() 方法的第一个参数，将所有映射的目标字符作为 maketrans() 方法的第二个参数即可。例如，直接在交互式解释器中执行如下代码：

>>> table = str.maketrans('abt','αβτ')
>>> table
{97: 945, 98: 946, 116: 964}
>>> table = str.maketrans('abc','123')
>>> table
{97: 49, 98: 50, 99: 51}
>>>

从上面的执行过程可以看到，不管是自己定义的翻译映射表，还是使用 maketrans() 方法创建的翻译映射表，其实都是为了定义字符与字符之间的对应关系，只不过该翻译映射表不能直接使用字符本身，必须使用字符的编码而已。

需要指出的是，如果使用 Python 2.x, str 类并没有 maketrans() 方法，而是由 string 模块提供 maketrans() 函数，因此程序需要先导入 string 模块，然后调用该模块的 maketrans() 函数。

下面在 Python 2.x 的交互式解释器中执行如下代码：

import string
table= string.maketrans('abc','123')

但 Python 2.x 中的翻译映射表不如 Python 3.x 的翻译映射表直观、明了，我们一般不用理会翻译映射表的内容，只要将翻译映射表作为 translate() 方法的参数即可。

## Python 字符串分割、连接方法

Python 还为 str 提供了分割和连接方法：

*   split()：将字符串按指定分割符分割成多个短语。
*   join()：将多个短语连接成字符串。

下面代码示范了上面两个方法的用法：

```
s = 'crazyit.org is a good site'
# 使用空白对字符串进行分割
print(s.split()) # 输出 ['crazyit.org', 'is', 'a', 'good', 'site']
# 使用空白对字符串进行分割,最多只分割前 2 个单词
print(s.split(None, 2)) # 输出 ['crazyit.org', 'is', 'a good site']
# 使用点进行分割
print(s.split('.')) # 输出 ['crazyit', 'org is a good site']
mylist = s.split()
# 使用'/'为分割符，将 mylist 连接成字符串
print('/'.join(mylist)) # 输出 crazyit.org/is/a/good/site
# 使用','为分割符，将 mylist 连接成字符串
print(','.join(mylist)) # 输出 crazyit.org,is,a,good,site
```

从上面的运行结果可以看出，str 的 split() 和 join() 方法互为逆操作。split() 方法用于将字符串分割成多个短语，而 join() 方法则用于将多个短语连接成字符串。