# Linux 环境变量

在 Linux 中，环境变量是一个很重要的概念。环境变量可以由系统、用户、Shell 以及其他程序来设定。

变量就是一个可以被赋值的字符串，赋值范围包括数字、文本、文件名、设备以及其他类型的数据。

下面的例子，我们将为变量 TEST 赋值，然后使用 **echo** 命令输出：

```
$TEST="Linux Programming"
$echo $TEST
Linux Programming
```

注意：变量赋值时前面不能加 $ 符号，变量输出时必须要加 $ 前缀。退出 Shell 时，变量将消失。

登录系统后，Shell 会有一个初始化的过程，用来设置环境变量。这个阶段，Shell 会读取 /etc/profile 和 .profile 两个文件，过程如下：

*   Shell 首先检查 /etc/profile 文件是否存在，如果存在，就读取内容，否则就跳过，但是不会报错。
*   然后检查你的主目录（登录目录）中是否存在 .profile 文件，如果存在，就读取内容，否则就跳过，也不会报错。

读取完上面两个文件，Shell 就会出现 $ 命令提示符：

```
$
```

出现这个提示符，就可以输入命令并调用相应的程序了。

注意：上面是 Bourne Shell 的初始化过程，bash 和 ksh 在初始化过程中还会检查其他文件。

## .profile 文件

/etc/profile 文件包含了通用的 Shell 初始化信息，由 Linux 管理员维护，一般用户无权修改。

但是你可以修改主目录下的 .profile 文件，增加一些“私人定制”初始化信息，包括：

*   设置默认终端类型和外观样式；
*   设置 Shell 命令查找路径，即 PATH 变量；
*   设置命令提示符。

找到主目录下的 .profile 文件，使用 vi 编辑器打开并查看内容。

## 设置终端类型

一般情况下，我们使用的终端是由 login 或 getty 程序设置的，可能会不符合我们的习惯。

对于没有使用过的终端，可能会比较生疏，不习惯命令的输出样式，交互起来略显吃力。所以，一般用户会将终端设置成下面的类型：

```
$TERM=vt100
$
```

vt100 是 virtual terminate 100 的缩写。虚拟终端是一种假的终端，真正有自己的显示器和键盘的终端，会通过特殊电缆（如串口）连到计算机主机。vt100 是被绝大多数 Linux 系统所支持的一种虚拟终端规范，常用的还有 ansi、xterm 等。

## 设置 PATH 变量

在命令提示符下输入一个命令时，Shell 会根据 PATH 变量来查找该命令对应的程序，PATH 变量指明了这些程序所在的路径。

一般情况下 PATH 变量的设置如下：

```
$PATH=/bin:/usr/bin
$
```

多个路径使用冒号(:)分隔。如果用户输入的命令在 PATH 设置的路径下没有找到，就会报错，例如：

```
$hello
hello: not found
$
```

## PS1 和 PS2 变量

PS1 变量用来保存命令提示符，可以随意修改，如果你不习惯使用 $ 作为提示符，也可以改成其他字符。PS1 变量被修改后，提示符会立即改变。

例如，把命令提示符设置成'=>'：

```
$PS1='=>'
=>
=>
=>
```

也可以将提示信息设置成当前目录，例如：

```
=>PS1="[\u@\h \w]\$"
[root@ip-72-167-112-17 /var/www/tutorialspoint/Linux]$
[root@ip-72-167-112-17 /var/www/tutorialspoint/Linux]$
```

命令提示信息包含了用户名、主机名和当前目录。

下表中的转义字符可以被用作 PS1 的参数，丰富命令提示符信息。

| 转义字符 | 描述 |
| \t | 当前时间，格式为 HH:MM:SS |
| \d | 当前日期，格式为 Weekday Month Date |
| \n | 换行 |
| \W | 当前所在目录 |
| \w | 当前所在目录的完整路径 |
| \u | 用户名 |
| \h | 主机名（IP 地址） |
| # | 输入的命令的个数，每输入一个新的命令就会加 1 |
| \$ | 如果是超级用户 root，提示符为#，否则为$。 |

你可以在每次登录的时候修改提示符，也可以在 .profile 文件中增加 PS1 变量，这样每次登录时会自动修改提示符。

如果用户输入的命令不完整，Shell 还会使用第二提示符来等待用户完成命令的输入。默认的第二命令提示符是 >，保存在 PS2 变量，可以随意修改。

下面的例子使用默认的第二命令提示符：

```
$ echo "this is a
> test"
this is a
test
$
```

下面的例子通过 PS2 变量改变提示符：

```
$ PS2="secondary prompt->"
$ echo "this is a
secondary prompt->test"
this is a
test
$
```

## 常用环境变量

下表列出了部分重要的环境变量，这些变量可以通过上面提到的方式修改。

| 变量 | 描述 |
| DISPLAY | 用来设置将图形显示到何处。 |
| HOME | 当前用户的主目录。 |
| IFS | 内部域分隔符。 |
| LANG | LANG 可以让系统支持多语言。例如，将 LANG 设为 pt_BR，则可以支持(巴西)葡萄牙语。 |
| PATH | 指定 Shell 命令的路径。 |
| PWD | 当前所在目录，即 cd 到的目录。 |
| RANDOM | 生成一个介于 0 和 32767 之间的随机数。 |
| TERM | 设置终端类型。 |
| TZ | 时区。可以是 AST(大西洋标准时间)或 GMT(格林尼治标准时间)等。 |
| UID | 以数字形式表示的当前用户 ID，shell 启动时会被初始化。 |

下面的例子中使用了部分环境变量：

```
$ echo $HOME
/root
]$ echo $DISPLAY

$ echo $TERM
xterm
$ echo $PATH
/usr/local/bin:/bin:/usr/bin:/home/amrood/bin:/usr/local/bin
$
```