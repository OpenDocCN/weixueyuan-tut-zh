# shell printf 命令：格式化输出语句

printf 命令用于格式化输出， 是 echo 命令的增强版。它是 C 语言 printf()库函数的一个有限的变形，并且在语法上有些不同。

注意：printf 由 POSIX 标准所定义，移植性要比 echo 好。

如同 echo 命令，printf 命令也可以输出简单的字符串：

```
$printf "Hello, Shell\n"
Hello, Shell
$
```

printf 不像 echo 那样会自动换行，必须显式添加换行符(\n)。

printf 命令的语法：

```
printf  format-string  [arguments...]
```

format-string 为格式控制字符串，arguments 为参数列表。

printf()在 C 语言入门教程中已经讲到，功能和用法与 printf 命令类似，请查看：[C 语言格式输出函数 printf()详解](http://c.biancheng.net/cpp/html/33.html)

这里仅说明与 C 语言 printf()函数的不同：

*   printf 命令不用加括号
*   format-string 可以没有引号，但最好加上，单引号双引号均可。
*   参数多于格式控制符(%)时，format-string 可以重用，可以将所有参数都转换。
*   arguments 使用空格分隔，不用逗号。

请看下面的例子：

```
# format-string 为双引号
$ printf "%d %s\n" 1 "abc"
1 abc
# 单引号与双引号效果一样 
$ printf '%d %s\n' 1 "abc" 
1 abc
# 没有引号也可以输出
$ printf %s abcdef
abcdef
# 格式只指定了一个参数，但多出的参数仍然会按照该格式输出，format-string 被重用
$ printf %s abc def
abcdef
$ printf "%s\n" abc def
abc
def
$ printf "%s %s %s\n" a b c d e f g h i j
a b c
d e f
g h i
j
# 如果没有 arguments，那么 %s 用 NULL 代替，%d 用 0 代替
$ printf "%s and %d \n" 
and 0
# 如果以 %d 的格式来显示字符串，那么会有警告，提示无效的数字，此时默认置为 0
$ printf "The first program always prints'%s,%d\n'" Hello Shell
-bash: printf: Shell: invalid number
The first program always prints 'Hello,0'
$
```

注意，根据 POSIX 标准，浮点格式%e、%E、%f、%g 与%G 是“不需要被支持”。这是因为 awk 支持浮点预算，且有它自己的 printf 语句。这样 Shell 程序中需要将浮点数值进行格式化的打印时，可使用小型的 awk 程序实现。然而，内建于 bash、ksh93 和 zsh 中的 printf 命令都支持浮点格式。