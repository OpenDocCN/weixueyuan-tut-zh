# Python __getitem__、__setitem__、__delitem__、__len__、__contains__ 用法

序列最重要的特征就是可包含多个元素， 因此和序列有关的特殊方法有如下几个：

*   __len__(self)：该方法的返回值决定序列中元素的个数。
*   __getitem__(self, key)：该方法获取指定索引对应的元素。该方法的 key 应该是整数值或 slice 对象，否则该方法会引发 KeyError 异常。
*   __contains__(self, item)：该方法判断序列是否包含指定元素。
*   __setitem__(self, key, value)：该方法设置指定索引对应的元素。该方法的 key 应该是整数值或 slice 对象，否则该方法会引发 KeyError 异常。
*   __delitem__(self, key)：该方法删除指定索引对应的元素。

如果程序要实现不可变序列（程序只能获取序列中的元素，不能修改），只要实现上面前 3 个方法就行；如果程序要实现可变序列（程序既能获取序列中的元素，也可修改），则需要实现上面
5 个方法。

下面程序将会实现一个字符串序列，在该字符串序列中默认每个字符串的长度都是 3，该序列的元素按 AAA、 AAB、 AAC…… 这种格式排列：

```
def check_key (key):
    '''
    该函数将会负责检查序列的索引，该索引必须是整数值，否则引发 TypeError
    且程序要求索引必须为非负整数，否则引发 IndexError
    '''
    if not isinstance(key, int): raise TypeError('索引值必须是整数')
    if key < 0: raise IndexError('索引值必须是非负整数')
    if key >= 26 ** 3: raise IndexError('索引值不能超过%d' % 26 ** 3)  
class StringSeq:
    def __init__(self):
        # 用于存储被修改的数据
        self.__changed = {}
        # 用于存储已删除元素的索引
        self.__deleted = []
    def __len__(self):
        return 26 ** 3
    def __getitem__(self, key):
        '''
        根据索引获取序列中元素
        '''
        check_key(key)
        # 如果在 self.__changed 中找到已经修改后的数据
        if key in self.__changed :
            return self.__changed[key]
        # 如果 key 在 self.__deleted 中，说明该元素已被删除
        if key in self.__deleted :
            return None
        # 否则根据计算规则返回序列元素
        three = key // (26 * 26)
        two = ( key - three * 26 * 26) // 26
        one = key % 26
        return chr(65 + three) + chr(65 + two) + chr(65 + one)
    def __setitem__(self, key, value):
        '''
        根据索引修改序列中元素
        '''
        check_key(key)
        # 将修改的元素以 key-value 对的形式保存在 __changed 中
        self.__changed[key] = value
    def __delitem__(self, key):
        '''
        根据索引删除序列中元素
        '''
        check_key(key)
        # 如果 __deleted 列表中没有包含被删除 key，添加被删除的 key
        if key not in self.__deleted : self.__deleted.append(key)
        # 如果 __changed 中包含被删除 key，删除它
        if key in self.__changed : del self.__changed[key]
# 创建序列
sq = StringSeq()
# 获取序列的长度，实际上就是返回 __len__()方法的返回值
print(len(sq))
print(sq[26*26])
# 打印没修改之后的 sq[1]
print(sq[1]) # 'AAB'
# 修改 sq[1]元素
sq[1] = 'fkit'
# 打印修改之后的 sq[1]
print(sq[1]) # 'fkit'
# 删除 sq[1]
del sq[1]
print(sq[1]) # None
# 再次对 sq[1]赋值
sq[1] = 'crazyit'
print(sq[1]) # crazyit
```

上面程序实现了一个 StringSeq 类，并为该类实现了一个 __len__()、 __getitem__()、 __setitem__() 和 __delitem__() 方法，其中 __len__() 方法返回该序列包含的元素个数，__getitem__() 方法根据索引返回元素，__setitem__() 方法根据索引修改元素的值，而 __delitem__() 方法则用于根据索引删除元素。

该序列本身并不保存序列元素，序列会根据索引动态计算序列元素，因此该序列需要保存被修改、被删除的元素。该序列使用 __changed 实例变量保存被修改的元素，使用 __deleted 实例变量（列表）保存被删除的索引。

在定义了字符串序列之后， 接下来程序创建了序列对象，并调用序列方法测试该工具类。运行该程序，可以看到如下输出结果：

17576
BAA
AAB
fkit
None
crazyit

从上面的输出结果来看，程序中序列的第二个元素 sq[1] 恰好为 'AAB'，程序既可对序列元素赋值，也可删除、修改序列元素，这完全是一个功能完备的序列。