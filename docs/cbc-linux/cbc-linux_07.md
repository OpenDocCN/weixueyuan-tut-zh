# Linux 管道和过滤器

有时候，我们可以把两个命令连起来使用，一个命令的输出作为另一个命令的输入，这就叫做**管道**。为了建立管道，需要在两个命令之间使用竖线(|)连接。

管道是 Linux 进程之间一种重要的通信机制；除了管道，还有共享内存、消息队列、信号、套接字(socket) 等进程通信机制。

管道使用竖线(|)将两个命令隔开，竖线左边命令的输出就会作为竖线右边命令的输入。连续使用竖线表示第一个命令的输出会作为第二个命令的输入，第二个命令的输出又会作为第三个命令的输入，依此类推。

能够接受数据，过滤（处理或筛选）后再输出的工具，称为**过滤器**。

## grep 命令

grep 是一个强大的文本搜索工具，可以使用正则表达式，并返回匹配的行，语法为：

```
$grep pattern file(s)
```

“grep”源于 ed（Linux 的一个行文本编辑器）的 g/re/p 命令，g/re/p 是“globally search for a regular expression and print all lines containing it”的缩写，意思是使用正则表达式进行全局检索，并把匹配的行打印出来。

正则表达式是一个包含了若干特殊字符的字符串，每个字符都有特殊含义，可以用来匹配文本，更多信息请查看正则表达式教程。

grep 可以看做是一个过滤器，如果没有为 grep 指定要检索的文件，那么它会从标准输入设备（一般是键盘）读取；其他过滤器也是如此。

grep 命令最简单的使用就是检索包含固定字符的文本。

例如，在管道中使用 grep 命令，只允许包含指定字符的行输出到显示器：

```
$ls -l | grep "Aug"
-rw-rw-rw-   1 john  doc     11008 Aug  6 14:10 ch02
-rw-rw-rw-   1 john  doc      8515 Aug  6 15:30 ch07
-rw-rw-r--   1 john  doc      2488 Aug 15 10:51 intro
-rw-rw-r--   1 carol doc      1605 Aug 23 07:35 macros
$
```

grep 命令有很多选项：

| 选项 | 说明 |
| -v | 反转查询，输出不匹配的行。例如，grep -v "test" demo.txt 将输出不包含"test"的行。 |
| -n | 输出匹配的行以及行号。 |
| -l | 输出匹配的行所在的文件名。 |
| -c | 输出匹配的总行数。 |
| -i | 不区分大小写进行匹配。 |

下面我们使用正则表达式来匹配这样的行：包含字符“carol”，然后包含任意数目（含零个）的其他字符，最后还要包含“Aug”。

使用 -i 选项进行不区分大小写的匹配：

```
$ls -l | grep -i "carol.*aug"
-rw-rw-r--   1 carol doc      1605 Aug 23 07:35 macros
$
```

## sort 命令

sort 命令在 Linux 中非常有用，它将文件中的各行按字母或数进行排序。sort 命令既可以从特定的文件，也可以从 stdin 获取输入。

例如，对 foot 文件的各行进行排序：

```
$sort food
Afghani Cuisine
Bangkok Wok
Big Apple Deli
Isle of Java
Mandalay
Sushi and Sashimi
Sweet Tooth
Tio Pepe's Peppers
$
```

通过下面的选项可以控制排序规则：

| 选项 | 描述 |
| -n | 按照数字大小排序，例如，10 会排在 2 后面；-n 选项会忽略空格或 tab 缩进。 |
| -r | 降序排序。sort 默认是升序排序。 |
| -f | 不区分大小写。 |
| +x | 对第 x 列（从 0 开始）进行排序。 |

下面的例子通过管道将 ls、grep 和 sort 命令连起来使用，过滤包含“Aug”的行，并按照文件大小排序：

```
$ls -l | grep "Aug" | sort +4n
-rw-rw-r--  1 carol doc      1605 Aug 23 07:35 macros
-rw-rw-r--  1 john  doc      2488 Aug 15 10:51 intro
-rw-rw-rw-  1 john  doc      8515 Aug  6 15:30 ch07
-rw-rw-rw-  1 john  doc     11008 Aug  6 14:10 ch02
$
```

上面的命令，对当前目录中八月份修改的文件按照大小排序；+4n 表示对第 5 列按照数字大小排序。

## pg 和 more 命令

如果文件内容过多，全部显示会很乱，可以使用 **pg** 和 **more** 命令分页显示，每次只显示一屏。

例如，通过管道，使用 more 命令显示目录中的文件：

```
$ls -l | grep "Aug" | sort +4n | more
-rw-rw-r--  1 carol doc      1605 Aug 23 07:35 macros
-rw-rw-r--  1 john  doc      2488 Aug 15 10:51 intro
-rw-rw-rw-  1 john  doc      8515 Aug  6 15:30 ch07
-rw-rw-r--  1 john  doc     14827 Aug  9 12:40 ch03
.
.
.
-rw-rw-rw-  1 john  doc     16867 Aug  6 15:56 ch05
--More--(74%)
```

如上，一次只显示一屏文本，显示满后，停下来，并提示已显示全部内容的百分比，按空格键(space)可以查看下一屏，按 b 键可以查看上一屏。