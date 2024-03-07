# Python 统计字母出现的频率

> 原文：[`www.weixueyuan.net/a/424.html`](http://www.weixueyuan.net/a/424.html)

使用 Python 统计 26 个字母出现的频率：输入是一个很长的字符串，对其中的英文字母统计其出现的次数，其他字符忽略不处理，大写字母和小写字母当作一个字母来看。

```

input_str = """The First-ever Open-water
Beluga Sanctuary Will Welcome Two Adorable Whales in June,
Adorable beluga whales are a popular attraction to aquariums
around the world, but like many other wild animals,
they also risk losing their habitats due to human
intervention such as population growth, new buildings
along the coastline, fishing, and other problems
that sea creatures face.
"""  # 输入的字符串
def calc_statistic(input_str):  # 计算英文字母出现的频率
    result = [0] * 26      # 构建结果列表
    for c in input_str:    # 对于每个输入字符串中的字符
        if c.isalpha():    # 必须是 26*2 个字符之一
            c = c.lower()  # 统一转换成小写字符
            index = ord(c) - ord('a')   # 计算出其对应的位置
            result[index] = result[index] + 1   # 将出现的次数加一
    for ele in range(0, 26):                    # 显示打印结果
        c = chr(ord('a') + ele)                 # 将位置转换成字符
        print("[%s] Shows Up %d Times" % (c, result[ele]))  # 显示结果
calc_statistic(input_str)  # 进行处理
```

输出结果如下：
$ python calcCharFreq.py
[a] Shows Up 35 Times  # a 字符出现的次数
[b] Shows Up 8 Times   # b 字符出现的次数
[c] Shows Up 7 Times
[d] Shows Up 8 Times
[e] Shows Up 32 Times  # e 字符出现的次数
[f] Shows Up 3 Times
[g] Shows Up 7 Times
[h] Shows Up 15 Times
[i] Shows Up 20 Times
[j] Shows Up 1 Times
[k] Shows Up 2 Times    # k 字符出现的次数
[l] Shows Up 21 Times
[m] Shows Up 6 Times
[n] Shows Up 20 Times
[o] Shows Up 22 Times
[p] Shows Up 6 Times
[q] Shows Up 1 Times
[r] Shows Up 21 Times
[s] Shows Up 18 Times
[t] Shows Up 27 Times
[u] Shows Up 15 Times
[v] Shows Up 2 Times
[w] Shows Up 10 Times
[x] Shows Up 0 Times
[y] Shows Up 3 Times
[z] Shows Up 0 Times    # z 字符出现的次数