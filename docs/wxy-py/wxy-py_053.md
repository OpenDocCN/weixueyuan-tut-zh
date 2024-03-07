# Python time 模块的用法

> 原文：[`www.weixueyuan.net/a/629.html`](http://www.weixueyuan.net/a/629.html)

在实际应用中，经常需要得到一些时间信息，如得到某个事件发生的时间、某个资源的有效期限及某个时间是否是周末等。

在 Python 中，有一个系统自带的库 time，在该库中提供了对时间、日期进行操作的一些工具函数。

time 库不是自动加载的。如果需要使用它，应先将其引入，代码如下：

import time

## 时间表示法

在 Python 中使用了两种时间表示法，一种是用距离 1970 年 1 月 1 日 0 时的秒数来表示时间。另外一种是常用的表示方法，用年、月、日、时、分、秒等来表示，后文称为时分秒表示。

如果用秒数来表示，则可以使用 time()函数来得到当前时间。

```

>>> import time
>>> time.time()
1555891997.224464
```

如果用时分秒来表示时间，可以使用 localtime() 函数来获得当前的本地时间。

```

>>> import time
>>> time.localtime()
time.struct_time(tm_year=2019, tm_mon=4, tm_mday=22, tm_hour=8,
tm_min=10, tm_sec=31, tm_wday=0, tm_yday=112, tm_isdst=0)
```

通过上面的例子可以看到，年份是用整数表示的，如 2019；月份也是用整数表示的，取值范围是 1~12，分别表示一月到十二月；日也是用整数表示的，取值范围是 1~31，分别对应 1 日到 31 日；时也是用整数表示的，范围是 0~59；分也是用整数表示的，范围是 0~59；秒也是用整数表示的，范围是 0~59。

另外，字段 tm_wday 表示星期几，取值范围是 0~6，0 表示星期一，6 表示星期日。字段 tm_yday 表示这个时间在当年是第多少天，取值范围是 1~366，1 表示是当年的第一天；tm_isdst 表示夏令时，可以取值 -1、0 或者 1。

这两种表示方法是可以相互转换的。如从秒数表示的时间转换到用时分秒表示的时间，可以使用 gmtime()函数。

```

>>> t = 0                              # 时间为 0，表示 1970 年 1 月 1 日 0 时
>>> time.gmtime(t)                     # 转换成年月日的样式
time.struct_time(tm_year=1970, tm_mon=1, tm_mday=1, tm_hour=0,
tm_min=0, tm_sec=0, tm_wday=3, tm_yday=1, tm_isdst=0)
```

如果要从时分秒表示的时间转换成秒数表示的时间，可以使用 mktime() 函数。

```

>>> t = 100                                    # 秒数表示的时间
>>> dmt = time.gmtime(t)                       # 转换成时分秒的格式
>>> dmt                                        # 显示
time.struct_time(tm_year=1970, tm_mon=1, tm_mday=1, tm_hour=0,
tm_min=1, tm_sec=40, tm_wday=3, tm_yday=1, tm_isdst=0)
>>> st = time.mktime(dmt)                      # 将时分秒转换成秒数
>>> st                                         # 注意是当地时间，我们的时区是+8
-28700.0
>>> st+8*60*60                                 # 调整时区，得到秒数
100.0
```

## 接口函数

接下在介绍一些和时间相关的接口函数，包括获得目前时间、得到不同格式的时间字符串等。

#### 1) time()：得到当前时间

time() 函数返回当前时间距离 1970 年 1 月 1 日 0 时的时间，单位为秒，返回值类型是浮点数。

```

import time
>>> time.time()      # 得到当前时间
1555891351.948157
```

#### 2) sleep（秒数）：休眠

sleep() 函数让当前程序休眠指定的秒数，其参数可以是浮点数。

```

>>> import time                    # 引入时间库
>>> t1 =  time.time()              # 得到当前时间 t1
>>> time.sleep(3)                  # 休眠 3 秒钟
>>> t2 =  time.time()              # 得到新的当前时间 t2
>>> print(t2-t1)                   # t2 和 t1 的时间差，大致就是休眠的时间
3.00099992752                      # 的确休眠了 3 秒
>>> t1 =  time.time()              # 第二个实验，得到新的当前时间 t1
>>> time.sleep(3.2)                # 休眠 3.2 秒，注意是实数，不是整数
>>> t2 =  time.time()              # 得到新的当前时间 t2
>>> print(t2-t1)                   # 查看二者时间差是否为 3.2 秒
3.20399999619                      # 时间差为 3.2 秒
```

#### 3) gmtime()：秒数格式转换成时分秒格式

gmtime() 函数返回一个类 time.struct_time 的实例对象，该对象包含 tm_year、tm_hour 等属性，分别用来表示年、小时等信息。

```

>>> a = time.time()
>>> a
1559053302.312
>>> b = time.gmtime(a)
>>> b
time.struct_time(tm_year=2019, tm_mon=5,
  tm_mday=28, tm_hour=14, tm_min=21,
  tm_sec=42, tm_wday=1, tm_yday=148,
  tm_isdst=0)
>>> type(b)
<type 'time.struct_time'>
>>> b.tm_year
2019
>>> b.tm_mon
5
```

#### 4) localtime()：得到当前时间（时分秒格式）

localtime() 函数得到时分秒表示的当前时间，并且是用类 time.struct_time 的实例对象来表示的。该实例对象包含 tm_year 等属性，用来表示年份等信息。

```

>>> a = time.localtime()
>>> type(a)
<type 'time.struct_time'>
>>> a
time.struct_time(tm_year=2019, tm_mon=5,
  tm_mday=28, tm_hour=22, tm_min=25,
  tm_sec=28, tm_wday=1, tm_yday=148,
  tm_isdst=0)
>>> a.tm_year
2019
>>> a.tm_mday
28
```