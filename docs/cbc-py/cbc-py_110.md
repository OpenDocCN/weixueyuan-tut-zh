# Python os 模块及用法（看了无师自通）

os 模块代表了程序所在的操作系统，主要用于获取程序运行所在操作系统的相关信息。

在 Python 的交互式解释器中先导入 os 模块，然后输入 os.__all__ 命令（__all__ 变量代表了该模块开放的公开接口），即可看到该模块所包含的全部属性和函数。

开发者同样不需要完全记住这些属性和函数的含义，在需要用时可参考[`docs.python.org/3/library/os.html`](https://docs.python.org/3/library/os.html)。

此处仅介绍 os 模块中常用的属性和函数：

*   os.name：返回导入依赖模块的操作系统名称，通常可返回 'posix'、'nt'、 'java' 等值其中之一。
*   os.environ：返回在当前系统上所有环境变量组成的字典。
*   os.fsencode(filename)：该函数对类路径（path-like）的文件名进行编码。
*   os.fsdecode(filename)：该函数对类路径（path-like）的文件名进行解码。
*   os.PathLike：这是一个类，代表一个类路径（path-like）对象。
*   os.getenv(key, default=None）：获取指定环境变量的值。
*   os.getlogin()：返回当前系统的登录用户名。与该函数对应的还有 os.getuid()、os.getgroups()、os.getgid() 等函数，用于获取用户 ID、用户组、组 ID 等，这些函数通常只在 UNIX 系统上有效。
*   os.getpid()：获取当前进程 ID。
*   os.getppid()：获取当前进程的父进程 ID。
*   os.putenv(key, value)：该函数用于设置环境变量。
*   os.cpu_count()：返回当前系统的 CPU 数量。
*   os.sep：返回路径分隔符。
*   os.pathsep：返回当前系统上多条路径之间的分隔符。一般在 Windows 系统上多条路径之间的分隔符是英文分号（;）；在 UNIX 及类 UNIX 系统（如 Linux、Mac os X）上多条路径之间的分隔符是英文冒号（:）。
*   os.linesep：返回当前系统的换行符。一般在 Windows 系统上换行符是“\r\n”：在 UNIX 系统上换行符是“\n”；在 Mac os X 系统上换行符是“\r”。
*   os.urandom(size)：返回适合作为加密使用的、最多由 N 个字节组成的 bytes 对象。该函数通过操作系统特定的随机性来源返回随机字节，该随机字节通常是不可预测的，因此适用于绝大部分加密场景。

下面程序示范了 os 模块的大部分函数的用法：

```
import os
# 显示导入依赖模块的操作系统的名称
print(os.name)
# 获取 PYTHONPATH 环境变量的值
print(os.getenv('PYTHONPATH'))
# 返回当前系统的登录用户名
print(os.getlogin())
# 获取当前进程 ID
print(os.getpid())
# 获取当前进程的父进程 ID
print(os.getppid())
# 返回当前系统的 CPU 数量
print(os.cpu_count())
# 返回路径分隔符
print(os.sep)
# 返回当前系统的路径分隔符
print(os.pathsep)
# 返回当前系统的换行符
print(os.linesep)
# 返回适合作为加密使用的、最多 3 个字节组成的 bytes
print(os.urandom(3))
```

运行上面程序，可以看到如下输出结果：

nt
None
yeeku
9904
12036
8
\
;

b'\x12\x1e\xcf'

从上面的输出结果可以看出，在 Windows 系统上 Python 导入依赖模块的操作系统名称为“nt”；当前系统的登录用户名是“yeeku”：当前进程 ID 为“9904”；当前进程的父进程 ID 为“12036”；当前系统上有 8 个 CPU；当前系统（Windows）的路径分隔符是“\”；当前系统（Windows）上多条路径之间的分隔符是分号（;）；但在当前系统（Windows）上换行符不能明显看到，这是因为当在控制台输出“\r\n” 时才会产生两个空行。

此外，在 os 模块下还包含大量操作文件和目录的功能函数，本教程将会在后续章节专门介绍这些功能函数。

在 os 模块下还包含各种进程管理函数，它们可用于启动新进程、中止己有进程等。在 os 模块下与进程管理相关的函数如下：

*   os.abort()：生成一个 SIGABRT 信号给当前进程。在 UNIX 系统上，默认行为是生成内核转储；在 Windows 系统上，进程立即返回退出代码 3。
*   os.execl(path, arg0, arg1, ...）：该函数还有一系列功能类似的函数，比如 os.execle()、os.execlp() 等，这些函数都是使用参数列表 arg0, arg1,...来执行 path 所代表的执行文件的。

    由于 os.exec*() 函数都是 PosIX 系统的直接映射，因此如采使用该命令来执行 Python 程序，传入的 arg0 参数没有什么作用。os._exit(n) 用于强制退出 Python 解释器。将其放在 try 决中可以阻止 finally 块的执行。

*   os.forkpty()：fork 一个子进程。
*   os.kill(pid, sig)：将 sig 信号发送到 pid 对应的过程，用于结束该进程。
*   os.killpg(pgid, sig)：将 sig 信号发送到 pgid 对应的进程组。
*   os.popen(cmd, mode='r', buffering=-1)：用于向 cmd 命令打开读写管道（当 mode 为 r 时为只读管道，当 mode 为 rw 时为读写管道），buffering 缓冲参数与内置的 open() 函数有相同的含义。该函数返回的文件对象用于读写字符串，而不是字节。
*   os.spawnl(mode, path, ...)：该函数还有一系列功能类似的函数，比如 os.spawnle()、os.spawnlp() 等，这些函数都用于在新进程中执行新程序。
*   os.startfile(path[,operation])：对指定文件使用该文件关联的工具执行 operation 对应的操作。如果不指定 operation 操作，则默认执行打开（open）操作。operation 参数必须是有效的命令行操作项目，比如 open（打开）、edit（编辑）、print（打印）等。
*   os.system(command)：运行操作系统上的指定命令。

下面程序示范了在 os 模块中与进程管理相关的函数的功能：

```
import os
# 运行平台上的 cmd 命令
os.system('cmd')
# 使用 Excel 打开 g:\abc.xls 文件
os.startfile('g:\\abc.xls')
os.spawnl(os.P_NOWAIT, 'E:\\Tools\\编辑工具\\Notepad++.7.5.6.bin.x64\\notepad++.exe', ' ')
# 使用 python 命令执行 os_test.py 程序
os.execl("D:\\Python\\Python36\\python.exe", " ", 'os_test.py', 'i')
```

如果直接运行上面程序，可以看到程序运行后使用 Excel 打开了 abe.xls 文件，也打开了 Notepad++ 工具，还使用 python 命令运行了 os_test.py 文件。但如果将程序中粗体字代码取消注释，将看到程序运行后只是启动了 cmd 命令行程序，这是因为使用 as.system() 函数来运行程序时，新程序所在的进程会替代原有的进程。

在使用 os.execl() 函数时新进程之后，也会取代原有的进程，因此上面程序将这行代码放在了最后。