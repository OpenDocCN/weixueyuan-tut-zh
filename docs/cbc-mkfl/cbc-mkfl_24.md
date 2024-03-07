# Makefile 中常见的错误信息

> 原文：[`c.biancheng.net/view/7161.html`](http://c.biancheng.net/view/7161.html)

make 执行过程中所产生错误并不都是致命的，特别是在命令行之前存在 "-"、或者 make 使用 "-k" 选项执行时。make 执行过程的致命错误都带有前缀字符串 "***"。错误信息都有前缀，一种是执行程序名作为错误前缀（通常是 "make"）；另外一种是当 Makefile 本身存在语法错误无法被 make 解析并执行时，前缀包含了 Makefile 文件名和出现错误的行号。

在下述的错误列表中，省略了普通前缀：

[FOO] Error NN
[FOO] signal description

这类错误并不是 make 的真正错误。它表示 make 检测到 make 所调用的作为执行命令的程序返回一个非零状态（Error NN），或者此命令程序以非正常方式退出（携带某种信号）。
如果错误信息中没有附加 "***" 字符串，则是子过程的调用失败，如果 Makefile 中此命令有前缀 "-"，make 会忽略这个错误。

missing separator. Stop.
missing separator (did you mean TAB instead of 8 spaces?). Stop.

错误的原因：不可识别的命令行，make 在读取 Makefile 过程中不能解析其中包含的内容。GNU make 在读取 Makefile 时根据各种分隔符（:, =, [TAB]字符等）来识别 Makefile 的每一行内容。这些错误意味着 make 不能发现一个合法的分隔符。
出现这些错误信息的可能的原因是（或许是编辑器，绝大部分是 ms- windows 的编辑器）在 Makefile 中的命令之前使用了 4 个（或者 8 个）空格代替了 [Tab] 字符。这种情况，将产生上述的第二种形式产生错误信息。且记，所有的命令行都应该是以 [Tab] 字符开始的。

commands commence before first target. Stop.
missing rule before commands. Stop.

Makefile 可能是以命令行开始：以 [Tab] 字符开始，但不是一个合法的命令行（例如，一个变量的赋值）。命令行必须和规则一一对应。
产生第二种的错误的原因可能是一行的第一个非空字符为分号，make 会认为此处遗漏了规则的 "target: prerequisite" 部分。

No rule to make target 'XXX'.
No rule to make target 'XXX ', needed by 'yyy'.

无法为重建目标“XXX”找到合适的规则，包括明确规则和隐含规则。
修正这个错误的方法是：在 Makefile 中添加一个重建目标的规则。其它可能导致这些错误的原因是 Makefile 中文件名拼写错误，或者破坏了源文件树（一个文件不能被重建，可能是由于依赖文件的问题）。

No targets specified and no makefile found. Stop.
No targets. Stop.

第一个错误表示在命令行中没有指定需要重建的目标，并且 make 不能读入任何 Makefile 文件。第二个错误表示能够找到 Makefile 文件，但没有终极目标或者没有在命令行中指出需要重建的目标。这种情况下，make 什么也不做。

Makefile 'XXX' was not found.
Included makefile 'XXX' was not found.

没有使用 "-f" 指定 Makefile 文件，make 不能在当前目录下找到默认 Makefile（makefile 或者 GNUmakefile）。使用 "-f" 指定文件，但不能读取这个指定的 Makefile 文件。

warning: overriding commands for target 'XXX'
warning: ignoring old commands for target 'XXX'

对同一目标 "XXX" 存在一个以上的重建命令。GNU make 规定：当同一个文件作为多个规则的目标时，只能有一个规则定义重建它的命令（双冒号规则除外）。如果为一个目标多次指定了相同或者不同的命令，就会产生第一个告警；第二个告警信息说新指定的命令覆盖了上一次指定的命令。

Circular XXX <- YYY dependency dropped.

规则的依赖关系产生了循环：目标 "XXX" 的依赖文件为 "YYY"，而依赖 "YYY" 的依赖列表中又包含 "XXX"。

Recursive variable 'XXX' references itself (eventually). Stop.

make 的变量 "XXX"（递归展开式）在替换展开时，引用它自身。无论对于直接展开式变量（通过:=定义的）或追加定义（+=），这都是不允许的。

Unterminated variable reference. Stop.

变量或者函数引用语法不正确，没有使用完整的的括号（缺少左括号或者右括号）。

insufficient arguments to function 'XXX'. Stop.

函数 "XXX" 引用时参数数目不正确。函数缺少参数。

missing target pattern. Stop.
multiple target patterns. Stop.
target pattern contains no '%'. Stop.             
mixed implicit and static pattern rules. Stop.

不正确的静态模式规则。
第一条错误的原因是：静态模式规则的目标段中没有模式目标；
第二条错误的原因是：静态模式规则的目标段中存在多个模式目标；
第三条错误的原因是：静态模式规则的目标段目标模式中没有包含模式字符“%”；
第四条错误的原因是：静态模式规则的三部分都包含了模式字符“%”。正确的应该是只有后两个才可以包含模式字符“%”。

warning: -jN forced in submake: disabling jobserver mode.

这一条告警和下条告警信息发生在：make 检测到递归的 make 调用时，可通信的子 make 进程出现并行处理的错误。递归执行的 make 的命令行参数中存在 "-jN" 参数（N 的值大于 1），在有些情况下可能导致此错误，例如：Makefile 中变量 "MAKE" 被赋值为 "make –j2"，并且递归调用的命令行中使用变量 "MAKE"。在这种情况下，被调用 make 进程不能和其它 make 进程进行通信，其只能简单的独立的并行处理两个任务”。

warning: jobserver unavailable: using -j1\. Add '+' to parent make rule.

为了现实 make 进程之间的通信，上层 make 进程将传递信息给子 make 进程。在传递信息过程中可能存在这种情况，子 make 进程不是一个实际的 make 进程，而上层 make 却不能确定子进程是否是真实的 make 进程。它只是将所有信息传递下去。上层 make 采用正常的算法来决定这些。当出现这种情况，子进程只会接受父进程传递的部分有用的信息。子进程会产生该警告信息，之后按照其内建的顺序方式进行处理。