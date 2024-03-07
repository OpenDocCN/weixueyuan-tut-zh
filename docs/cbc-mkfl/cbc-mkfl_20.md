# make 命令参数和选项大汇总

> 原文：[`c.biancheng.net/view/7126.html`](http://c.biancheng.net/view/7126.html)

我们在在执行 make 命令时，有的时候需要加上一下参数选项来保证我们的程序的执行，其实之前已经遇到过 make 在执行命令的时候需要添加上参数选项，比如只打印命令但不执行使用的参数是 "-n" ，还有只执命令不打印命令的参数选项是 "-s"，包含其它文件的路径参数选项是 "-include"等等。

我们现在列举一下 make 可以使用的参数选项，以及它们的功能是什么。

| 参数选项 | 功能 |
| -b，-m | 忽略，提供其他版本 make 的兼容性 |
| -B，--always-make | 强制重建所有的规则目标，不根据规则的依赖描述决定是否重建目标文件。 |
| -C DIR，--directory=DIR | 在读取 Makefile 之前，进入到目录 DIR，然后执行 make。当存在多个 "-C" 选项的时候，make 的最终工作目录是第一个目录的相对路径。 |
| -d | make 在执行的过程中打印出所有的调试信息，包括 make 认为那些文件需要重建，那些文件需要比较最后的修改时间、比较的结果，重建目标是用的命令，遗憾规则等等。使用 "-d" 选项我们可以看到 make 构造依赖关系链、重建目标过程中的所有的信息。 |
| --debug[=OPTIONS] | make 执行时输出调试信息，可以使用 "OPTIONS" 控制调试信息的级别。默认是 "OPTIONS=b" ，"OPTIONS" 的可值为以下这些，首字母有效：all、basic、verbose、implicit、jobs、makefile。 |
| -e，--enveronment -overrides | 使用环境变量定义覆盖 Makefile 中的同名变量定义。 |
| -f=FILE，--file=FILE， --makefile=FILE | 指定文件 "FILE" 为 make 执行的 Makefile 文件 |
| -p，--help | 打印帮助信息。 |
| -i，--ignore-errors | 执行过程中忽略规则命令执行的错误。 |
| -I DIR，--include-dir=DIR | 指定包含 Makefile 文件的搜索目录，在 Makefile 中出现另一个 "include" 文件时，将在 "DIR" 目录下搜索。多个 "-i" 指定目录时，搜索目录按照指定的顺序进行。 |
| -j [JOBS]，--jobs[=JOBS] | 可指定同时执行的命令数目，爱没有 "-j" 的情况下，执行的命令数目将是系统允许的最大可能数目，存在多个 "-j" 目标时，最后一个目标指定的 JOBS 数有效。 |
| -k，--keep-going | 执行命令错误时不终止 make 的执行，make 尽最大可能执行所有的命令，直至出现知名的错误才终止。 |
| -l load，--load-average=[=LOAD]，--max-load[=LOAD] | 告诉 make 在存在其他任务执行的时候，如果系统负荷超过 "LOAD"，不在启动新的任务。如果没有指定 "LOAD" 的参数  "-l" 选项将取消之前 "-l" 指定的限制。 |
| -n，--just-print，--dry-run | 只打印执行的命令，但是不执行命令。 |
| -o FILE，--old-file=FILE， --assume-old=FILE | 指定 "FILE"文件不需要重建，即使是它的依赖已经过期；同时不重建此依赖文件的任何目标。注意：此参数不会通过变量 "MAKEFLAGS" 传递给子目录进程。 |
| -p，--print-date-base | 命令执行之前，打印出 make 读取的 Makefile 的所有数据，同时打印出 make 的版本信息。如果只需要打印这些数据信息，可以使用 "make -qp" 命令，查看 make 执行之前预设的规则和变量，可使用命令 "make -p -f /dev/null" |
| -q，-question | 称为 "询问模式" ；不运行任何的命令，并且无输出。make 只返回一个查询状态。返回状态 0 表示没有目标表示重建，返回状态 1 表示存在需要重建的目标，返回状态 2 表示有错误发生。 |
| -r，--no-builtin-rules | 取消所有的内嵌函数的规则，不过你可以在 Makefile 中使用模式规则来定义规则。同时选项 "-r" 会取消所有后缀规则的隐含后缀列表，同样我们可以在 Makefile 中使用 ".SUFFIXES"，定义我们的后缀名的规则。"-r" 选项不会取消 make 内嵌的隐含变量。 |
| -R，--no-builtin-variabes | 取消 make 内嵌的隐含变量，不过我们可以在 Makefile 中明确定义某些变量。注意："-R" 和 "-r" 选项同时打开，因为没有了隐含变量，所以隐含规则将失去意义。 |
| -s，--silent，--quiet | 取消命令执行过程中的打印。 |
| -S，--no-keep-going， --stop | 取消 "-k" 的选项在递归的 make 过程中子 make 通过 "MAKEFLAGS" 变量继承了上层的命令行选项那个。我们可以在子 make 中使用“-S”选项取消上层传递的 "-k" 选项，或者取消系统环境变量 "MAKEFLAGS" 中 "-k"选项。 |
| -t，--touch | 和 Linux 的 touch 命令实现功能相同，更新所有的目标文件的时间戳到当前系统时间。防止 make 对所有过时目标文件的重建。 |
| -v，version | 查看 make 的版本信息。 |
| -w，--print-directory | 在 make 进入一个子目录读取 Makefile 之前打印工作目录，这个选项可以帮助我们调试 Makefile，跟踪定位错误。使用 "-C" 选项时默认打开这个选项。 |
| --no-print-directory | 取消 "-w" 选项。可以是 用在递归的 make 调用的过程中 ，取消 "-C" 参数的默认打开 "-w" 的功能。 |
| -W FILE，--what-if=FILE， --new-file=FILE，
--assume-file=FILE | 设定文件 "FILE" 的时间戳为当前的时间，但不更改文件实际的最后修改时间。此选项主要是为了实现对所有依赖于文件 "FILE" 的目标的强制重建。 |
| --warn-undefined-variables | 在发现 Makefile 中存在没有定义的变量进行引用时给出告警信息。此功能可以帮助我们在调试一个存在多级嵌套变量引用的复杂 Makefile。但是建议在书写的时候尽量避免超过三级以上的变量嵌套引用。 |