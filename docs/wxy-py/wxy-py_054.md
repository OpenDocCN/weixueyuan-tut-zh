# Python datatime 模块的用法

> 原文：[`www.weixueyuan.net/a/631.html`](http://www.weixueyuan.net/a/631.html)

日期库 datatime 包括 date 类、datetime 类和 time 类等，这些类能够完成不同的功能。该库不用安装，仅需引入即可，方式如下：

import datetime

datetime.date 类包含下面的接口函数。

#### 1) today()：得到现在的时间信息

today() 函数返回当前的时间信息，包括日期和时间信息。

```

>>> a = datetime.datetime.today()
>>> a
datetime.datetime(2019, 5, 28, 22, 32, 56, 221000)
```

#### 2) weekday()：得到某天是星期几

weekday() 返回值是一个整数，取值范围是 0～6，0 表示星期一，1 表示星期二，6 表示星期日。

```

# 构造一个日期，2019 年 2 月 23 日
>>> date1 = datetime.date(2019, 2, 23)
>>> date1.weekday()            # 这天是星期几
5                              # 星期六
```

另外，也可以使用 isoweekday() 来得到这样的星期几信息，其返回值也是一个整数，但取值范围为 1~7，星期一用 1 来表地，星期日用 7 来表示。

```

# 构造一个日期，2019 年 2 月 23 日
>>> date1 = datetime.date(2019, 2, 23)
>>> date1.isoweekday()               # 这天星期几
6                                    # 星期六
```

#### 3) ctime()：转换成字符串格式

该函数将日期转换成字符串格式。

```

# 构造一个日期，2019 年 2 月 23 日
>>> date1 = datetime.date(2019, 2, 23)
>>> date1.ctime()
'Sat Feb 23 00:00:00 2019'
```