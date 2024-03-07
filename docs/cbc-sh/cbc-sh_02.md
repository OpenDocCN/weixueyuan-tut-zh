# 几种常见的 Shell

上面提到过，Shell 是一种脚本语言，那么，就必须有解释器来执行这些脚本。

Unix/Linux 上常见的 Shell 脚本解释器有 bash、sh、csh、ksh 等，习惯上把它们称作一种 Shell。我们常说有多少种 Shell，其实说的是 Shell 脚本解释器。

## bash

bash 是 Linux 标准默认的 shell，本教程也基于 bash 讲解。bash 由 Brian Fox 和 Chet Ramey 共同完成，是 BourneAgain Shell 的缩写，内部命令一共有 40 个。

Linux 使用它作为默认的 shell 是因为它有诸如以下的特色：

*   可以使用类似 DOS 下面的 doskey 的功能，用方向键查阅和快速输入并修改命令。
*   自动通过查找匹配的方式给出以某字符串开头的命令。
*   包含了自身的帮助功能，你只要在提示符下面键入 help 就可以得到相关的帮助。

## sh

sh 由 Steve Bourne 开发，是 Bourne Shell 的缩写，sh 是 Unix 标准默认的 shell。

## ash

ash shell 是由 Kenneth Almquist 编写的，Linux 中占用系统资源最少的一个小 shell，它只包含 24 个内部命令，因而使用起来很不方便。

## csh

csh 是 Linux 比较大的内核，它由以 William Joy 为代表的共计 47 位作者编成，共有 52 个内部命令。该 shell 其实是指向/bin/tcsh 这样的一个 shell，也就是说，csh 其实就是 tcsh。

## ksh

ksh 是 Korn shell 的缩写，由 Eric Gisin 编写，共有 42 条内部命令。该 shell 最大的优点是几乎和商业发行版的 ksh 完全兼容，这样就可以在不用花钱购买商业版本的情况下尝试商业版本的性能了。

注意：bash 是 Bourne Again Shell 的缩写，是 linux 标准的默认 shell ，它基于 Bourne shell，吸收了 C shell 和 Korn shell 的一些特性。bash 完全兼容 sh，也就是说，用 sh 写的脚本可以不加修改的在 bash 中执行。