# Python os 模块用法详解

> 原文：[`www.weixueyuan.net/a/687.html`](http://www.weixueyuan.net/a/687.html)

在本章的最后，我们来介绍一下 os 模块中有关进程的一些接口函数。在前面已经用到了该模块一些接口函数，如得到当前进程的 PID。这些接口函数比较基础，在不太复杂的环境中也是非常有用的。

如果必须使用进程的一些高级特性，还是推荐使用 multiprocessing 模块或者 subprocess 模块。

## 1) system()：启动进程

可以使用 system() 接口函数来启动一个进程，格式如下：

os.system(命令字符串)

system() 接口函数接收一个字符串格式的命令，然后启动一个进程去运行该命令，并且在该命令返回之前，该接口函数是不会返回的。该函数的返回值是该命令的返回码。

这应该是最常用的一个接口函数，我们可以用其来运行任意的可执行文件或者脚本。注意参数是一个字符串，就是我们在 shell 中输入的内容。

```

>>> os.system("date")           # date 是命令
Mon Jul  1 15:23:27 CST 2019    # 这是 date 命令的输出
0                               # 这是 system()的返回值，就是 date 命令的返回码
```

我们可以在命令行字符串中带上参数，如下面的用法：

```

>>> os.system("echo hello python")
hello python
0
```

我们还可以使用重定向符、管道等，如下面的用法：

```

>>> os.system("ps | grep grep")
23645 ttys006    0:00.00 sh -c ps | grep grep
23647 ttys006    0:00.00 grep grep     
0                                             #这是命令的返回码，0 表示成功
```

总之，能够在 shell 中执行的任何命令都可以用这种方式来执行。

## 2) popen()：启动进程并得到输出

system() 接口函数只能够得到子进程的返回码，不能得到子进程的输出。如果希望得到子进程的输出信息，可以使用 open() 接口函数，该接口函数的定义如下：

os.popen(命令行参数字符串)

使用 popen() 可以解决得到标准输出信息这个问题，但是却得不到子进程的返回码。下面的例子演示了得到子进程的标准输出的方法。

```

>>> r = os.popen("date", "r", 1)        # 第二个参数 r 表示仅读出
>>> stdout = r.read()                   # 读出子进程的输出
>>> stdout                              # 查看输出
'Mon Jul  1 21:55:27 CST 2019\n'        # 这就是 date 命令的输出
```

如果希望为子进程提供输入，可以将第二个参数改为 w，就是我们可以为该子进程写入一些信息。

下面的例子使用了命令 cat-|grep python>a.txt，该命令的意思是从标准输入读入数据，然后通过 grep 来得到所有包含 python 的行，并将这样的行写入到文件 a.txt 中。

```

# 构造这个命令
>>> w = os.popen("cat - | grep python > a.txt", "w", 1)  # 给 cat -这个命令输入一行，注意最后的\n 表示换行符
>>> w.write("line1: abcd\n")
                              # 写入的字节数
>>> w.write("line2: love python\n")    # 给 cat -这个命令输入另外一行
                              # 写入的字节数
>>> w.close()                          # 关闭 w，相当于按下 Ctrl+D 组合键
>>> fd = open('a.txt', 'r')            # 打开文件 a.txt，即命令的输出
>>> fd.read()                          # 读出文件内容
'line2: love python\n'                 # 第二行被保存到 a.txt 中
>>> fd.close()                         # 关闭 a.txt
```

## 3) getpid()：得到当前进程 ID

该函数定义如下：

os.getpid()

该函数返回当前 Python 解释器的进程 ID。

```

>>> os.getpid()                        # 得到当前 Python 解释器的进程 ID
21028
>>> os.system("ps | grep python")      # 通过 ps 命令得到 Pyhton 相关的进程
21028 ttys006    0:00.17 python3       # 这就是当前的 Python 解释器对应的进程
24741 ttys006    0:00.00 grep python
25399 ttys006    0:00.00 sh -c ps | grep python
25401 ttys006    0:00.00 grep python
```

## 4) getppid()：得到父进程 ID

该函数返回当前 Python 解释器的父进程 ID。

```

>>> os.getppid()               # 得到当前 Python 解释器的父进程，就是一个 bash 进程
15747
>>> os.system("ps a | grep bash")      # 通过 ps 来查看相关的 bash 进程
15747 s006  S      0:00.11 -bash       # 这就是得到当前 Python 解释器的父进程
25427 s006  S+     0:00.00 sh -c ps a | grep bash
25429 s006  S+     0:00.00 grep bash
```

除了这些接口函数之外，在 Linux 或者 macOS 版的 Python 中还提供了其他的进程相关的接口函数，如 fork()、kill()、execv()，这些和 C 语言版的进程操作接口函数类似，但是仅在类 UNIX 系统中才存在，所以这些接口函数在 Windows 平台上并不存在，这里也不相关介绍。