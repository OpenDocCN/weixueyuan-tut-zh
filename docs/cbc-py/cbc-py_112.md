# Python time 模块详解

time 模块主要包含各种提供日期、时间功能的类和函数。该模块既提供了把日期、时间格式化为字符串的功能，也提供了从字符串恢复日期、时间的功能。

在 Python 的交互式解释器中先导入 time 模块，然后输入 `[e for e in dir(time) if not e.startswith('_')]` 命令，即可看到该模块所包含的全部属性和函数：

>>> [e for e in dir(time) if not e.startswith('_')]
['altzone', 'asctime', 'clock', 'ctime', 'daylight', 'get_clock_info', 'gmtime', 'localtime', 'mktime', 'monotonic', 'perf_counter', 'process_time', 'sleep', 'strftime', 'strptime', 'struct_time', 'time', 'timezone', 'tzname']

在 time 模块内提供了一个 time.struct_time 类，该类代表一个时间对象，它主要包含 9 个属性，每个属性的信息如表 1 所示：

表 1 time.struct_time 类中各属性的含义

| 字段名 | 字段含义 | 值 |
| tm_year | 年 | 如 2017、2018 等 |
| tm_mon | 月 | 如 2、3 等，范围为 1~12 |
| tm_mday | 日 | 如 2、3 等，范围为 1~31 |
| tm_hour | 时 | 如 2、3 等，范围为 0~23 |
| tm_min | 分 | 如 2、3 等，范围为 0~59 |
| tm_sec  | 秒 | 如 2、3 等，范围为 0~59 |
| tm_wday | 周 | 周一为 0，范围为 0~6 |
| tm_yday |  一年内第几天 | 如 65，范围 1~366 |
| tm_isdst | 夏时令 | 0、1 或 -1 |

比如，Python 可以用 time.struct_time(tm_year=2018, tm_mon=5, tm_mday=2, tm_hour=8, tm_min=0, tm_sec=30, tm_wday=3, tm_yday=1, tm_isdst=0) 很清晰地代表时间。

此外，Python 还可以用一个包含 9 个元素的元组来代表时间，该元组的 9 个元素和 struct_time 对象中 9 个属性的含义是一一对应的。比如程序可以使用（2018, 5, 2, 8, 0, 30, 3, 1, 0）来代表时间。

在日期、时间模块内常用的功能函数如下：

*   time.asctime([t])：将时间元组或 struct_time 转换为时间字符串。如果不指定参数 t，则默认转换当前时间。
*   time.ctime([secs])：将以秒数代表的时间转换为时间宇符串。

    Python 可以用从 1970 年 1 月 1 日 0 点整到现在所经过的秒数来代表当前时间，比如我们写 30 秒，那么意味着时间是 1970 年 1 月 1 日 0 点 0 分 30 秒。但需要注意的是，在实际输出时可能会受到时区的影响，比如中国处于东八区，因此实际上会输出 1970 年 1 月 1 日 8 点 0 分 30 秒。

*   time.gmtime([secs])：将以秒数代表的时间转换为 struct_time 对象。如果不传入参数，则使用当前时间。
*   time.localtime([secs])：将以秒数代表的时间转换为代表当前时间的 struct_time 对象。如果不传入参数，则使用当前时间。
*   time.mktime(t)：它是 localtime 的反转函数，用于将 struct_time 对象或元组代表的时间转换为从 1970 年 1 月 1 日 0 点整到现在过了多少秒。
*   time.perf_counter()：返回性能计数器的值。以秒为单位。
*   time.process_time()：返回当前进程使用 CPU 的时间。以秒为单位。
*   time.sleep(secs)：暂停 secs 秒，什么都不干。
*   time.strftime(format[, t])：将时间元组或 struct_time 对象格式化为指定格式的时间字符串。如果不指定参数 t，则默认转换当前时间。
*   time.strptime(string[, format])：将字符串格式的时间解析成 struct_time 对象。
*   time.time()：返回从 1970 年 1 月 1 日 0 点整到现在过了多少秒。
*   time.timezone：返回本地时区的时间偏移，以秒为单位。
*   time.tzname：返回本地时区的名字。

下面程序示范了 time 棋块的功能函数：

```
import time
# 将当前时间转换为时间字符串
print(time.asctime())
# 将指定时间转换时间字符串，时间元组的后面 3 个元素没有设置
print(time.asctime((2018, 2, 4, 11, 8, 23, 0, 0 ,0))) # Mon Feb  4 11:08:23 2018
# 将以秒数为代表的时间转换为时间字符串
print(time.ctime(30)) # Thu Jan  1 08:00:30 1970
# 将以秒数为代表的时间转换为 struct_time 对象。
print(time.gmtime(30))
# 将当前时间转换为 struct_time 对象。
print(time.gmtime())
# 将以秒数为代表的时间转换为代表当前时间的 struct_time 对象
print(time.localtime(30))
# 将元组格式的时间转换为秒数代表的时间
print(time.mktime((2018, 2, 4, 11, 8, 23, 0, 0 ,0))) # 1517713703.0
# 返回性能计数器的值
print(time.perf_counter())
# 返回当前进程使用 CPU 的时间
print(time.process_time())
#time.sleep(10)
# 将当前时间转换为指定格式的字符串
print(time.strftime('%Y-%m-%d %H:%M:%S'))
st = '2018 年 3 月 20 日'
# 将指定时间字符串恢复成 struct_time 对象。
print(time.strptime(st, '%Y 年%m 月%d 日'))
# 返回从 1970 年 1970 年 1 月 1 日 0 点整到现在过了多少秒。
print(time.time())
# 返回本地时区的时间偏移，以秒为单位
print(time.timezone) # 在国内东八区输出-28800
```

运行上面程序，可以看到如下输出结果：

Fri Feb 22 11:28:39 2019
Mon Feb  4 11:08:23 2018
Thu Jan  1 08:00:30 1970
time.struct_time(tm_year=1970, tm_mon=1, tm_mday=1, tm_hour=0, tm_min=0, tm_sec=30, tm_wday=3, tm_yday=1, tm_isdst=0)
time.struct_time(tm_year=2019, tm_mon=2, tm_mday=22, tm_hour=3, tm_min=28, tm_sec=39, tm_wday=4, tm_yday=53, tm_isdst=0)
time.struct_time(tm_year=1970, tm_mon=1, tm_mday=1, tm_hour=8, tm_min=0, tm_sec=30, tm_wday=3, tm_yday=1, tm_isdst=0)
1517713703.0
0.0
0.140625
2019-02-22 11:28:39
time.struct_time(tm_year=2018, tm_mon=3, tm_mday=20, tm_hour=0, tm_min=0, tm_sec=0, tm_wday=1, tm_yday=79, tm_isdst=-1)
1550806119.4960592
-28800

time 模块中的 strftime() 和 strptime() 两个函数互为逆函数，其中 strftime() 用于将 struct_time 对象或时间元组转换为时间字符串；而 strptime() 函数用于将时间字符串转换为 struct_time 对象。这两个函数都涉及编写格式模板，比如上面程序中使用 %Y 代表年、%m 代表月、%d 代表日、%H 代表时、%M 代表分、%S 代表秒。这两个函数所需要的时间格式字符串支持的指令如表 2 所示：

表 2 Python 时间格式字符串所支持的指令

| 指令 | 含义 |
| %a | 本地化的星期几的缩写名，比如 Sun 代表星期天 |
| %A | 本地化的星期几的完整名 |
| %b | 本地化的月份的缩写名，比如 Jan 代表一月 |
| %B | 本地化的月份的完整名 |
| %c | 本地化的日期和时间的表示形式 |
| %d | 代表一个月中第几天的数值，范固： 01~31 |
| %H | 代表 24 小时制的小时，范围：00~23 |
| %I | 代表 12 小时制的小时，范围：01~12 |
| %j | 一年中第几天，范围：001~366 |
| %m | 代表月份的数值，范围：01~12 |
| %M | 代表分钟的数值，范围：00~59 |
| %p | 上午或下午的本地化方式。当使用 strptime() 函数并使用 %I 指令解析小时时，%p 只影响小时字段 |
| %S | 代表分钟的数值，范围：00~61。该范围确实是 00~61，60 在表示闰秒的时间戳时有效，而 61 则是由于一些历史原因造成的 |
| %U | 代表一年中表示第几周，以星期天为每周的第一天，范围：00~53。在这种方式下，一年中第一个星期天被认为处于第一周。当使用 strptime() 函数解析时间字符串时，只有同时指定了星期几和年份该指令才会有效 |
| %w | 代表星期几的数值，范围：0~6，其中 0 代表周日 |
| %W | 代表一年小第几周，以星期一为每周的第一天，范围：00~53。在这种方式下，一年中第一个星期一被认为处于第一周。当使用 strptime() 函数解析时间字符串时，只有同时指定了星期几和年份该指令才会有效 |
| %x | 本地化的日期的表示形式 |
| %X | 本地化的时间的表示形式 |
| %y | 年份的缩写，范围：00~99，比如 2018 年就简写成 18 |
| %Y | 年份的完整形式。如 2018 |
| %z | 显示时区偏移 |
| %Z | 时区名（如果时区不行在，则显示为空） |
| %% | 用于代表%符号 |