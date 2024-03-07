# Python redis 模块操作字符串方法汇总

> 原文：[`www.weixueyuan.net/a/769.html`](http://www.weixueyuan.net/a/769.html)

本节主要介绍字符串数据的操作。

1) set()：该函数用于给单个键赋值。

```

>>> r.set('key10', 100)
True
>>> r.get('key10')                    # 得到键 key10 对应的值
b'100'                                # 返回值是字符串
```

该函数还有几个参数，其中包括有效时间，即如果过了指定时间，该键自动被删除。

可以用 ex 参数来指定该键存在的秒数。下面的代码设定一个键，其有效时间是 10 秒，可以发现在 10 秒后该键就不存在了。

```

import time, redis
r = redis.Redis(host='127.0.0.1',port=6379)
r.set("str_set_expire_demo", "val1", ex=10)
print("Key Exist? %s" % bool(r.exists("str_set_expire_demo")))
time.sleep(12)
print("12 Seconds Passed, Key Exist? %s" % bool(r.exists("str_set_expire_demo")))
```

运行后的结果如下：

$ python setEx1.py
Key Exist? True
12 Seconds Passed, Key Exist? False

如果希望存在的时间比较短，可以使用参数 px，其表示存在的毫秒数。下面的例子设置在 800 毫秒后就自动删除：

```

import time, redis
r = redis.Redis(host='127.0.0.1',port=6379)
r.set("str_set_expire_demo", "val1", px=800)
print("Key Exist? %s" % bool(r.exists("str_set_expire_demo")))
time.sleep(1)
print("1 Seconds Passed, Key Exist? %s" % bool(r.exists("str_set_
     expire_demo")))
```

运行后的结果如下：

$ python setEx2.py
Key Exist? True
1 Seconds Passed, Key Exist? False

如果希望 1.2 秒后就自动删除，可以设置为：

r.set("str_set_expire_demo", "val1", ex=1, px=1200)

如果希望仅在该键不存在时才进行赋值，则可以使用参数 nx=True：

```

>>> r.set("str_set_nx_demo", "val1")    # 设定了该值
True                                    # 设定成功
>>> r.exists('str_set_nx_demo')        # 键是否存在
1                                    # 存在
# 仅不存在时才赋值，没有赋值
>>> r.set("str_set_nx_demo", "val1", nx=True)
>>> r.set("str_set_nx_demo", "val1")    # 直接赋值，返回 True 表示赋值成功
True
```

如果希望仅在该键已经存在时才进行赋值，则可以使用参数 xx=True：

```

>>> r.exists('str_set_xx_demo')        # 键是否存在
0                                        # 不存在
>>> r.set("str_set_xx_demo", "val1", xx=True)        # 赋值，失败
>>> r.set("str_set_xx_demo", "val1")    # 直接赋值
True                                    # 返回值 True 表示赋值成功
>>> r.exists('str_set_xx_demo')        # 键是否存在
1                                        # 存在
>>> r.set("str_set_xx_demo", "val1", xx=True)        # 再次赋值，
True                                    # 成功
```

另外还有几个专有的赋值接口，如 setex(key,val,exval)，其等效于 set(key,val,ex=exval)；如 psetex(key,pxval,val)，其等效于 set(key,val,px=pxval)。

2) mset()：该函数给多个键赋值，其接收一个字典，输入字典的键和值都应该是字符串：

```

>>> dict_a = {'mset_key1': 'mset_val1', 'mset_key2': 'mset_val2'}
>>> r.mset(dict_a)
True
>>> r.get('mset_key1')
b'mset_val1'
>>> r.get('mset_key2')
b'mset_val2'
```

3) get（键）：该函数得到单个键的值，其输入参数是一个字符串。如果该键不存在，返回 None。

```

>>> r.exists('key1')                    # 是否存在键 key1
1                                        # 存在
>>> r.get('key1')                        # 得到键 key1 对应的值
b'12'
>>> r.exists('key100')                    # 是否存在键 key100
0                                        # 不存在
>>> ret = r.get('key100')                # 得到键 key100 对应的值
>>> ret is None                            # 返回值是 None
True
```

4) mget()：该函数得到一组键的值，其输入和输出都是列表。

```

>>> r.set("mget1", "mget_val1")
True
>>> r.set("mget2", "mget_val2")
True
>>> r.set("mget3", "mget_val3")
True
>>> r.mget(["mget1", "mget2", "mget3"])
[b'mget_val1', b'mget_val2', b'mget_val3']
```

5) getset()：该函数首先得到指定键原来的值，然后修改其内容，并返回原来的值。如果指定的键不存在，则返回 None。

```

>>> r.set("getset1", 10)                # 给 getset1 赋值 10
True                                    # 成功
>>> r.get("getset1")                    # 得到 getset1 的值
b'10'
>>> r.getset("getset1", 1000)            # 得到 getset1 的旧值并赋新值 1000
b'10'                                    # 旧值为 10
>>> r.get("getset1")                    # 修改后的值为 1000
b'1000'
>>> r.exists("getset100")                # getset100 不存在
0
>>> r.getset("getset100", "abc")        # 这时返回的是 None
>>> r.get("getset100")                    # 赋值成功
b'abc'
```

6) setrange（键，开始位置，新值）：该函数用新的值来替换从开始位置开始的同等长度的子字符串。如果原始字符串长度不够，可以认为在最后一个字符后面跟着无限长的 null 字符，这样就不存在被替代的子字符串不够长的问题了。该函数返回修改后的字符串的长度。

```

>>> r.set("setrange1", "12345")        # 第一个例子，原始值
True
>>> r.setrange("setrange1", 3, "abcdefghijk")    # 从第三个开始的子字符串
                                    # 新字符串的长度为 14
>>> r.get("setrange1")                    # 新字符串的值
b'123abcdefghijk'
>>> r.set("setrange2", "1234567890")    # 第二个例子，原始值
True
>>> r.setrange("setrange2", 3, "ax")    # 修改第三和第四个字符
10
>>> r.get("setrange2")                    # 新字符串的值
b'123ax67890'
>>> r.set("setrange3", "12")            # 第三个例子
True
>>> r.setrange("setrange3", 4, "abcd")    # 原来值里面没有第四个字符
                            # 所以可以认为第三和第四个字符是 null 字符
>>> r.get("setrange3")
b'12\x00\x00abcd'
```

7) 修改指定的位 setbit（键，位置，值）：该函数将每个字符转换成对应的 ASCII 码，然后连接起来。例如字符串 python 对应的二进制值为 01110000 01111001 01110100 0110 1000 01101111 01101110，下面是得到这个二进制流（也称为位流）的代码。

```

>>> a = "python"              # 计算字符串对应的二进制流
>>> for c in a:
...   print(bin(ord(c)))
...                           # for 循环结束
0b1110000
0b1111001
0b1110100
0b1101000
0b1101111
0b1101110
```

我们可以修改其中任意一位。例如将第一个字符 p 对应的 01110000 修改为 01110001，便得到一个新的值 q，这样该键对应的值就变成了 qython。下面的代码演示了这个过程：

```

>>> r.set("setbit1", "python")
True
>>> r.setbit("setbit1", 7, 1)
0
>>> r.get("setbit1")
b'qython'
```

下面介绍如何将中文转换成对应的位流。中文使用 utf-8 的编码方式，例如“中”字在 utf-8 中用三个字节表示，分别是 e4、b8、ad，所以其对应的二进制流就是 11100100 1011 1000 10101101。下面是计算这个二进制流的方法：

```

>>> chinese_c = "中"
>>> chinese_c.encode("utf-8")
b'\xe4\xb8\xad'
>>> for c in chinese_c.encode("utf-8"):
...   print(bin(c))
...                                     # for 循环结束
0b11100100                            # 二进制内容输出
0b10111000
0b10101101
```

8) getbit（键，位置）：该函数先将字符串转换成二进制流，然后得到指定位的值。下面仍以前面的中文字符串为例来看看该函数的用法。

```

>>> r.set("getbit1", "中")
True
>>> r.getbit("getbit1", 1)
1
>>> r.getbit("getbit1", 7)
0
```

9) bitop（运算类型，结果存放地，操作数）：该函数用于进位运算。这里结果存放地和操作数都是键，就是说对操作数进行运算类型指定的操作，并将结果放到结果存放地。

运算类型用字符串表示，支持的操作包括 AND、OR、NOT 和 XOR。我们以“1”和“2”为操作数，它们对应的二进制流分别为 00110001 和 00110010。

```

>>> bin(ord("1"))
'0b110001'
>>> bin(ord("2"))
'0b110010'
```

下面演示 AND 操作，00110001 AND 00110010 应该得到的是 00110000：

```

>>> bin(ord("1"))                        # 得到字符 1 的 ASCII 码
'0b110001'                                # 二进制表示该 ASCII 码
>>> bin(ord("2"))                        # 对字符 2 进行同样的操作
'0b110010'
>>> r.set("op1", "1")                    # 设 op1 为 1
True
>>> r.set("op2", "2")
True
>>> r.bitop("AND", "result", "op1", "op2")        # 与操作
1
>>> x = r.get("result")
>>> x
b'0'
>>> bin(ord(x))                            # 得到二进制表示的结果
'0b110000'
```

下面演示 OR 操作，00110001 OR 00110010 应该得到的是 00110011：

```

# 对 op1 和 op2 做或操作，结果放入 result 中
>>> r.bitop("OR", "result", "op1", "op2")
1                                        #
>>> x = r.get("result")                    # 得到 result 的值
>>> x                                    # 查看 x 的值
b'3'
>>> bin(ord(x))
'0b110011'
```

下面演示 NOT 操作，NOT 00110001 得到的结果是 11001110：

```

>>> r.bitop("NOT", "result", "op1")
1
>>> x = r.get("result")
>>> x
b'\xce'
>>> bin(ord(x))
'0b11001110'
```

下面演示 XOR 操作，00110001 XOR 00110010 应该得到的是 00000011：

```

>>> r.bitop("XOR", "result", "op1", "op2")
1
>>> x = r.get("result")
>>> x
b'\x03'
>>> bin(ord(x))
'0b11'
```

10) strlen（键）：该函数得到字符串的长度，单位是字节，如果是中文的话，大于字符的个数。

```

>>> r.set("str_len", "abcd")
True
>>> r.strlen("str_len")
4
>>> r.set("str_len", "中文")        # 中文两个字符，但是却占了 6 个字节
True
>>> r.strlen("str_len")
6
```

11) incr（键，增加量）：如果指定的键不存在，就创建该键，并且值为增加量；如果指定的键已经存在，那么该键值等于原来的值加上增加量。返回值是指定的键操作后的值。注意，增加量只能是整数，不能是浮点数，如果希望用浮点数，则应该使用 incrbyfloat() 函数。

```

>>> r.exists("incr_demo1")
0
>>> r.incr("incr_demo1", 12)
12
>>> r.get("incr_demo1")
b'12'
>>> r.incr("incr_demo1", 12)
24
```

> 如果原来的值不能转换成数字，会抛出 redis.exceptions.ResponseError 异常。

12) decr（键，减少量）：和 incr() 类似，如果指定的键不存在，创建该键，赋值为减少量的相反数；如果指定的键已经存在，那么该键的值为原来值减去减少量的结果。

```

>>> r.exists("decr_obj1")
0
>>> r.decr("decr_obj1", 10)
-10
>>> r.get("decr_obj1")
b'-10'
>>> r.decr("decr_obj1", -88)
78
>>> r.get("decr_obj1")
b'78'
```

13) incrbyfloat（键，增加量）：和 incr() 类似，不过其增加量是浮点数。

```

>>> r.exists("incr_float_obj1")
0
>>> r.incrbyfloat("incr_float_obj1", 18.98)
18.98
>>> r.get("incr_float_obj1")
b'18.98'
>>> r.incrbyfloat("incr_float_obj1", -918.75)
-899.77
>>> r.get("incr_float_obj1")
b'-899.77000000000000002'
```

14) append（键，字符串）：该函数用于字符串的连接操作，例如，原来的值是“abc”，现在添加“xyz”，那么结果就是“abcxyz”。如果指定的键原来不存在，则创建该键，并且将其初始化为空字符串，然后执行 append() 操作。返回值是操作后字符串的字节数，例如“abc”后面添加“xyz”后返回的就是 6。

```

>>> r.exists("append_obj1")
0
>>> r.append("append_obj1", "abc")
3
>>> r.get("append_obj1")
b'abc'
>>> r.append("append_obj1", "xyz")
6
>>> r.get("append_obj1")
b'abcxyz'
>>> r.append("append_obj1", 897.98)
12
>>> r.get("append_obj1")
b'abcxyz897.98'
```