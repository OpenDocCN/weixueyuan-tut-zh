# Python 找出丢失的数

> 原文：[`www.weixueyuan.net/a/397.html`](http://www.weixueyuan.net/a/397.html)

有一个包含 1 到 100，一共 100 整数的列表 list，但是顺序已经被打乱，现在随机丢掉一个值，请计算出被扔掉的数的值。

## 1\. 直接的解法

依次查看 1 到 100 哪些数没有在输入的列表中。代码如下：

```

import random                        # 引入伪随机数模块
def find_missing_num(list):          # 查找丢失的数
    for candidate in range(1, 101):  # 对所有的数，查看是否在结果队列中
        if candidate not in list:    # 如果不在结果队列中，那么它就是丢失的数
            return candidate         # 返回该丢失的数
    return None                      # 如果没有找到，返回 None
def test():  # 测试代码
    list_a = range(1, 101)  # 得到 100 个数
    random.shuffle(list_a)  # 打乱顺序
    drop_val = list_a.pop(49)  # 丢掉一个数
    print("The Dropped Item is %d" % drop_val)
    ret = find_missing_num(list_a)  # 查找该丢失的数
    print("Answer: %d" % ret)
if __name__=='__main__':  # 如果是脚本运行而不是 import，那么执行测试代码
    test()
```

运行结果：

$ python listdemo3.py
The Dropped Item is 77
Answer: 77
$ python listdemo3.py
The Dropped Item is 97
Answer: 97

## 2\. 求和的方法

求出列表中所有元素的和 s，假定丢掉的元素为 x，那么 s+x=1+2+3+…+100。这样可以求出 x 的值来。代码如下：

```

import random  # 引入随机库
    def find_missing_num(list):
        expected_sum = ( (1 + 100) * 100) / 2
        real_sum = sum(list)
        return (expected_sum - real_sum)
    def test():
        list_a = range(1, 101)
        random.shuffle(list_a)
        drop_val = list_a.pop(49)
        print("The Dropped Item is %d" % drop_val)
        ret = find_missing_num(list_a)
print("Answer: %d" % ret)
    if __name__=='__main__':
        test()
```

运行结果：

$ python listdemo4.py
The Item is 87
Missing Number = 87
$ python listdemo4.py
The Dropped Item is 79
Answer: 79