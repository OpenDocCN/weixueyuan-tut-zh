# GDB help 命令：查看目标命令的具体用法

> 原文：[`c.biancheng.net/view/8293.html`](http://c.biancheng.net/view/8293.html)

截止到本节，我们接触了大量的 GDB 命令，甚至很多命令还拥有不同的语法格式和参数。这就产生一个问题，如何才能记住它们呢？

实际上，GDB 调试器的开发人员也想到了这个问题。为了降低用户使用 GDB 调试器的学习成本，GDB 提供了 help 命令，它可以帮用户打印出目标命令的功能和具体用法。

首先，为了方便用户能够快速地从众多 GDB 命令中查找到目标命令，help 命令根据不同 GDB 命令的功能对它们做了分类：

(gdb) help
List of classes of commands:

aliases -- Aliases of other commands
breakpoints -- Making program stop at certain points
data -- Examining data
files -- Specifying and examining files
internals -- Maintenance commands
obscure -- Obscure features
running -- Running the program
stack -- Examining the stack
status -- Status inquiries
support -- Support facilities
tracepoints -- Tracing of program execution without stopping the program
user-defined -- User-defined commands

可以看到，根据各个 GDB 命令的不同功能，help 命令将它们分成了 12 大类，每一类中都包含多个功能类似的 GDB 命令。

以 breakpoints 类为例，该类中包含了 GDB 所有的断点命令。借助 help 命令，我们可以查看某一类中具体包含的 GDB 命令：

(gdb) help breakpoints
Making program stop at certain points.

List of commands:

awatch -- Set a watchpoint for an expression
break -- Set breakpoint at specified location
break-range -- Set a breakpoint for an address range
catch -- Set catchpoints to catch events
catch assert -- Catch failed Ada assertions
catch catch -- Catch an exception
……

在此基础上，通过 help 命令，我们可以查看指定命令的功能和用法。例如：

(gdb) help break
Set breakpoint at specified line or function.
break [LOCATION] [thread THREADNUM] [if CONDITION]
LOCATION may be a line number, function name, or "*" and an address.
If a line number is specified, break at start of code for that line.
If a function is specified, break at start of code for that function.
If an address is specified, break at that exact address.
With no LOCATION, uses current execution address of the selected
stack frame.  This is useful for breaking on return to a stack frame.

THREADNUM is the number from "info threads".
CONDITION is a boolean expression.

Multiple breakpoints at one place are permitted, and useful if their
conditions are different.

Do "help breakpoints" for info on other commands dealing with breakpoints.

可以看到，我们尝试借助 help 命令查看 break 命令，其打印信息中包括 break 命令的具体功能、完整语法格式以及各个参数的具体含义。由此，只要系统学习过目标命令的读者，借助 help 命令打印出的提示信息，一定可以回忆起来并切实用目标命令开始对程序进行调试。

> 感兴趣的读者，可自行尝试使用 help 命令打印其他命令的使用信息，本节不再过多举例。

## GDB 自动补全命令

除了 help 命令辅助用户使用 GDB 调试器之外，GDB 还支持命令的自动补全。所谓自动补全，即在 (gdb) 右侧输入 GDB 命令时，对于特别长的命令，我们只需要输入命令的前几个字符，然后按下 Tab 键，GDB 就会自动帮我们补全整个命令。

举个例子：

(gdb) info bre<TAB>   

如上所示，当我们按下 Tab 键时，GDB 会自行将 bre 补全为 break。

需要注意的是，有些 GDB 命令长得很像，例如 condition 和 continue，此时如果我们只输入 con，GDB 是无法领会我们要使用哪个命令的。这种情况下，我们可以双击 Tab 键，GDB 会将所有可能的命令都罗列出来：

(gdb) con <Tab><Tab>
condition continue

由此可知在所有的 GDB 命令中，以 con 开头的命令有 2 个。

除此之外，当我们输入完成的 GDB 命令后，通过双击 Tab 键，GDB 会给我们罗列出该命令可用的所有参数。比如：

(gdb) catch <Tab><Tab>
assert     exception  fork       load       signal     throw      vfork
catch      exec       handlers   rethrow    syscall    unload