# Linux 打印文件和发送邮件

通过前面的介绍，相信你对 Linux 的命令和特性有了一个基本的认识，本节将介绍如果打印文件以及发送邮件。

## 文件打印

如果你希望打印文本文件，最好预先处理一下，包括调整边距、设置行高、设置标题等，这样打印出来的文件更加美观，易于阅读。当然，不处理也可以打印，但是可能会比较丑陋。

大部分的 Linux 自带了 nroff 和 troff 两个强大的文本格式化工具，不过比较老旧，使用的人很少，有兴趣的读者可以可以自行学习，本教程不再进行深入讲解。

#### pr 命令

**pr** 命令用来将文本文件转换成适合打印的格式，它可以把较大的文件分割成多个页面进行打印，并为每个页面添加标题。

**pr** 命令的语法如下：

```
pr option(s) filename(s)
```

**pr** 命令仅仅改变文件在屏幕上的显示样式和打印输出样式，并不会更改文件本身。下表是 pr 命令的几个选项：

| 选项 | 说明 |
| -k | 分成几列打印，默认为 1。 |
| -d | 两倍行距（并不是所有版本的 pr 都有效）。 |
| -h "header" | 设置每个页面的标题。 |
| -t | 不打印标题和上下边距。 |
| -l PAGE_LENGTH | 每页显示多少行。默认是每个页面一共 66 行，文本占 56 行。 |
| -o MARGIN | 每行缩进的空格数。 |
| -w PAGE_WIDTH | 多列输出时，设置页面宽度，默认是 72 个字符。 |

例如，food 文件包含了很多食品的名字，使用 pr 命令分成两列打印，并设置每页的标题为“Restaurants”。

首先查看文件内容：

```
$cat food
Sweet Tooth
Bangkok Wok
Mandalay
Afghani Cuisine
Isle of Java
Big Apple Deli
Sushi and Sashimi
Tio Pepe's Peppers
........
$
```

然后使用 **pr** 命令打印：

```
$pr -2 -h "Restaurants" food
Nov  7  9:58 1997  Restaurants   Page 1

Sweet Tooth              Isle of Java
Bangkok Wok              Big Apple Deli
Mandalay                 Sushi and Sashimi
Afghani Cuisine          Tio Pepe's Peppers
........
$
```

#### lp 和 lpr 命令

**lp** 和 **lpr** 命令将文件传送到打印机进行打印。使用 pr 命令将文件格式化后就可以使用这两个命令来打印。

打印机一般由系统管理员来设置，下面的例子使用默认的打印机打印 food 文件：

```
$lp food
request id is laserp-525  (1 file)
$
```

命令成功执行会返回一个表示打印任务的 ID，通过这个 ID 可以取消打印或者查看打印状态。

如果你希望打印多份文件，可以使用 lp 的 -n**Num** 选项，或者 lpr 命令的 -**Num** 选项。**Num** 是一个数字，可以随意设置。

如果系统连接了多台打印机，可以使用 lp 命令的 -d**printer** 选项，或者 lpr 命令的 -P**printer** 选项来选择打印机。**printer** 为打印机名称。

#### lpstat 和 lpq 命令

**lpstat** 命令可以查看打印机的缓存队列（有多少个文件等待打印），包括任务 ID、所有者、文件大小、请求时间和请求状态。

提示：等待打印的文件会被放到打印机的的缓存队列中。

例如，使用 **lpstat -o** 命令查看打印机中所有等待打印的文件，包括你自己的：

```
$lpstat -o
laserp-573  john  128865  Nov 7  11:27  on laserp
laserp-574  grace  82744  Nov 7  11:28
laserp-575  john   23347  Nov 7  11:35
$
```

lpstat -o 命令按照打印顺序输出队列中的文件。

**lpq** 命令显示的信息与 lpstat -o 稍有差异：

```
$lpq
laserp is ready and printing
Rank   Owner      Job  Files                  Total Size
active john       573  report.ps              128865 bytes
1st    grace      574  ch03.ps ch04.ps        82744 bytes
2nd    john       575  standard input         23347 bytes
$
```

第一行为打印机的状态。如果打印机无法使用或者纸被用完，将会输出其他信息。

#### cancel 和 lprm 命令

**cancel** 和 **lprm** 分别用来终止 lp 和 lpr 的打印请求。使用这两个命令，需要指定 ID（由 lp 或 lpq 返回）或打印机名称。

例如，通过 ID 取消打印请求：

```
$cancel laserp-575
request "laserp-575" cancelled
$
```

如果希望取消正在打印的文件，那么可以不指定 ID，仅仅指定打印机名称即可：

```
$cancel laserp
request "laserp-573" cancelled
$
```

lprm 命令用来取消当前用户的正在等待打印的文件，使用任务号作为参数可以取消指定文件，使用横线(-)作为参数可以取消所有文件。

例如，取消 575 号打印任务：

```
$lprm 575
dfA575diamond dequeued
cfA575diamond dequeued
$
```

lprm 会返回被取消的文件名。

## 发送邮件

可以使用**mail**命令发送和接收邮件，语法如下：

```
$mail [-s subject] [-c cc-addr] [-b bcc-addr] to-addr
```

每个选项的含义如下：

| 选项 | 描述 |
| -s | 邮件标题。 |
| -c | 要发送的用户，多个用户以逗号(,)分隔。 |
| -b | 需要密件发送（密送）的用户，多个用户以逗号(,)分隔。 |

例如，向 admin@yahoo.com 发送邮件：

```
$mail -s "Test Message" admin@yahoo.com
Hello everyone, 
this is Linux tutorial and url is http://see.xidian.edu.cn/cpp/linux/.
Cc: 
```

第一行是输入的命令，-s 表示邮件的主题，后面的 admin@yahoo.com 则是邮件的接收人，输入完这行命令后回车，会进入邮件正文的编写，你可以输入任何文字，比如上面的两行。输入完邮件正文，需要按 CTRL+D 结束输入，此时会提示你输入 Cc 地址，即邮件抄送地址，没有直接回车就完成了邮件的发送。

也可以通过重定向操作符 < 来发送文件：

```
$mail -s "Report 05/06/07" admin@yahoo.com < demo.txt
```

通过上面的命令，就可以把 demol.txt 文件的内容作为邮件的内容发送给 admin@yahoo.com 了。

接收邮件不需要任何参数：

```
$mail
no email
```