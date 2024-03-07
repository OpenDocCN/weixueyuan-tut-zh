# Python subprocess 模块用法详解

> 原文：[`www.weixueyuan.net/a/681.html`](http://www.weixueyuan.net/a/681.html)

在 Python 2.7 及 Python 3 中，系统自带了 subprocess 模块，该模块主要用来管理子进程。

在使用该模块之前需要将其引入，方法如下：

import subprocess

在以前，启动一个新的进程可以使用 os.system() 方法。相对来说 subprocess 模块提供了更多的灵活性，可以完全取代 os.system()。下面的例子是编写一个 shell 脚本，该脚本的返回值就是输入的参数。

```

#! /bin/bash
echo "sub process is running"    # 在标准输出中显示一行
if [ $# != 1 ]                   # 如果没有带上参数，返回值是 0
then
        exit 0
else                             # 否则返回值就是参数
        exit $1
fi
```

运行结果如下：

$ ./callSubprocess.sh 1        # 指定了返回值为 1
sub process is running
$ echo $?                      # 查看返回值
1
$ ./callSubprocess.sh 3        # 指定了返回值为 3
sub process is running
$ echo $?                      # 查看返回值
3
$ ./callSubprocess.sh
sub process is running
$ echo $?
0

下面使用 subprocess.call() 来启动进程。该接口函数接收一个参数列表，分别表示该命令的各个部分，如命令为 ls-l，则可以用 ["ls",'-l'] 来表示该命令。

```

>>> import subprocess
# 运行命令，如 bash callSubprocess.sh
>>> subprocess.call(["bash", "callSubprocess.sh"])
sub process is running                          # 标准输出
0                                               # 进程返回值
# 运行某个命令 bash callSubprocess.sh 1
>>> subprocess.call(["bash", "callSubprocess.sh", "1"])
sub process is running                          # 标准输出
1                                               # 进程返回值
# 运行某个命令 bash callSubprocess.sh 3
>>> subprocess.call(["bash", "callSubprocess.sh", "3"])
sub process is running                          # 标准输出
3                                               # 进程返回值
```

除了 call() 之外，subprocess 模块还提供了其他的面向过程的接口函数。

## 面向过程的接口函数

所谓面向过程的接口函数，就是不需要创建类实例便可以直接使用的接口函数。

#### 1) call（命令行参数，超时时间）

该函数在前面使用过，其参数可以是一个字符串，也可以是一个列表。该函数会一直等待（或者最多等待指定的时间）子进程结束。该函数的返回值就是子进程的返回值，而且子进程的输出就输出到当前进程的输出上。

```

>>> retcode = subprocess.call("pwd")     # 使用字符串
/Users/love,python/work
>>> retcode                              # 子进程返回值
0
>>> retcode = subprocess.call(["cat", "a.txt"])   # 使用列表
Tue Jul  2 09:22:09 CST 2019
>>> retcode                              # 子进程返回值
0
```

#### 2) check_call（命令行参数，超时时间）

该函数的用法和 call() 一样，二者的区别是，如果子进程的返回值不为 0，则抛出异常 CalledProcessError。

下面用 rm 命令来删除一个不存在的文件，这时其返回值就是 1。

```

$ rm nonexist.txt
rm: nonexist.txt: No such file or directory
$ echo $?
1
```

下面用 subprocess 来完成这些操作，代码如下：

```

# 执行前面第 1 行的命令
>>> retcode = subprocess.call(["rm", "nonexist.txt"])
rm: nonexist.txt: No such file or directory             # 子进程的输出
>>> retcode                                            # 查看返回值，就是前面第 3 行的命令
1
# 使用 check_call()
>>> retcode = subprocess.check_call(["rm", "nonexist.txt"])
rm: nonexist.txt: No such file or directory             # 子进程的输出
Traceback (most recent call last):                                      # 抛出了异常
  File "<stdin>", line 1, in <module>
  File "/anaconda3/lib/python3.7/subprocess.py", line 347, in check_call
    raise CalledProcessError(retcode, cmd)
  subprocess.CalledProcessError: Command '['rm', 'nonexist.txt']'
  returned non-zero exit status 1.
```

#### 3) check_output（命令行参数，超时时间）

该函数可以得到子进程的输出内容，其返回值就是子进程的输出。

```

>>> ret = subprocess.check_output("date")      # 得到子进程的输出
>>> ret                                        # 返回值就是子进程的输出
b'Tue Jul  2 13:29:41 CST 2019\n'
>>> type(ret)
<class 'bytes'>
```

如果希望同时得到标准输出和错误输出的数据，则可以加上参数 stderr=subprocess.STDOUT，这样错误输出和标准输出就合并了。例如可以使用 ls nonsexist 来查看一个不存在的目录，这时就会在错误输出中得到一行信息，但是在标准输出中是没有信息的。下面的 shell 执行过程演示了这个情况。

首先创建一个 shell 脚本，内容如下：

```

echo "stdout content"             # 在标准输出中输出一行
echo "stderr content" 1>&2        # 在错误输出中输出一行
exit 0                            # 返回值为 0
```

使用下面的方法来使用该脚本：

```

# 将标准输出定向到 stdout.txt，错误输出定向到 stderr.txt
$ ./stdout_err.sh 1>stdout.txt 2>stderr.txt
$ echo $?                         # 查看返回值
0
$ cat stdout.txt                  # 查看标准输出的内容
stdout content
$ cat stderr.txt                  # 查看错误输出的内容
stderr content
```

如果不合并错误输出到标准输出，那么情况如下：

```

>>> ret = subprocess.check_output(["bash", "./stdout_err.sh"])
stderr content                    # 没有捕获到的错误输出
>>> ret                           # 捕获到的标准输出
b'stdout content\n'
```

如果合并，可以看到下面的情况：

```

>>> ret = subprocess.check_output(["bash", "./stdout_err.sh"],
                                stderr=subprocess.STDOUT)
>>> ret                       # 输出包含标准输出和错误输出
b'stdout content\nstderr content\n'
```

需要注意的是，这个接口函数检查子进程的返回值，如果不为 0，则会抛出异常。例如，将前面的 shell 脚本修改一下，将其返回值改为 1，代码如下：

```

echo "stdout content"
echo "stderr content" 1>&2
exit 1                       # 返回值变成了 1
```

这时再运行该脚本，便可以看到下面的输出：

```

# 使用修改后的脚本
>>> ret = subprocess.check_output(["bash", "./stdout_err_2.sh"],
                           stderr=subprocess.STDOUT)
Traceback (most recent call last):      # 抛出了异常
  File "<stdin>", line 1, in <module>
  File "/anaconda3/lib/python3.7/subprocess.py", line 395, in check_output
    **kwargs).stdout
  File "/anaconda3/lib/python3.7/subprocess.py", line 487, in run
    output=stdout, stderr=stderr)
subprocess.CalledProcessError: Command '['bash', './stdout_err_2.sh']'
returned non-zero exit status 1.
>>> ret                                # 返回值是正常的
b'stdout content\nstderr content\n'
```

#### 4) getoutput()：仅得到输出而不检查返回值

我们会发现带有 check 的接口函数，都会检查返回值。如果不希望检查返回值，可以使用另外一个接口函数 getoutput()。该接口函数接收一个字符串命令，而且会另外启动一个 shell 来运行该命令。

```

>>> ret = subprocess.getoutput("./stdout_err_2.sh")
>>> ret                        # 子进程标准输出和错误输出的内容
'stdout content\nstderr content'
```

#### 5) getstatusoutput()：得到返回值和输出

如果希望同时得到返回值和输出的字符串，可以使用 getstatusoutput()。该函数接收一个字符串表示的命令，返回一个 tuple，第一个元素是返回值，第二个元素是标准输出和错误输出混合的字符串。

```

# 同时得到返回值和输出
>>> ret = subprocess.getstatusoutput("./stdout_err_2.sh")
>>> ret                  # 返回一个 tuple，第一个元素是返回值，第二个是输出
(1, 'stdout content\nstderr content')
```

## Popen 类

Popen 类包含很多功能，如得到子进程的返回值、查询子进程的状态等。该类的初始化函数包含非常多的参数，不过多数情况下仅使用第一个参数 args，该参数就是我们的命令行。

下面来创建一个 Popen 对象并启动其执行，方法如下：

```

>>> spp_obj = subprocess.Popen("date")
Mon Jul  1 23:05:27 CST 2019          # 子进程标准输出的内容
```

如果该命令包含一些参数，则可以将列表传入 args，如希望执行的是命令 echo"love Python"，则可以输入一个列表 ["echo","love Python"]，如下面的代码所示。

```

>>> spp_obj = subprocess.Popen(args=["echo", "love Python"])
love Python                       # 子进程的输出
```

比较常见的问题是，在 Linux 和 macOS 系统中，如果给 args 参数一个字符串，但是该字符串类似于 "cat a.txt"，这在执行时会报错，因为如果 args 是一个字符串，则期望整个字符串是一个可执行文件，而不是分段后的第一部分是可执行文件。这样便导致了找不到文件"cat a.txt"错误。所以如果可执行文件带有参数，一定要用列表传入。

> 但如果是在 Windows 平台上这样使用，就不存在这个问题，这是因为不同平台的实现方式是不同的。

另一个比较有用的参数是 shell，其默认是 False。这就要求 args 命令可以不用 shell 就能运行，如某个可执行文件 notepad.exe。但是如果要执行的是一个 shell 脚本，就需要将该参数设置为 True，这时其会启动一个 shell 进程，并在该 shell 进程中执行 shell 脚本。

下面介绍如何为子进程提供输入，以及如何得到子进程的输出。我们可以在创建时指定参数 stdin、stdout 来达到这些目的。

可以给这些参数赋值一个文件对象，这样子进程就会从指定的文件读入数据，或者将输出定向到指定的文件中。下面的例子就是将输出定向到某个文件的情况。

```

import time, os, sys
import subprocess
fd_stdout = open("a.txt", "w+") # 打开文件 a.txt 作为子进程的输出文件
po_obj = subprocess.Popen("date", shell=True, stdout=fd_stdout)
time.sleep(1)
fd_stdout.close()               # 关闭输出文件
fd_r = open("a.txt", "r")       # 查看 a.txt 的内容
content = fd_r.read()
print(u"a.txt 包含的内容：", content)
fd_r.close()
```

运行结果如下：

$ python3 sp_popen_stdout.py a.txt 包含的内容： Tue Jul 2 09:22:09 CST 2019

如果希望直接得到子进程的输出，而不是通过打开一个文件，则可以将 stdout 设定为 subprocess.PIPE，即设定为一个管道，然后便可以使用该子进程对象的 stdout 来读取子进程的输出了。还是上面的那个例子，可以将代码进行如下修改：

```

import time, os, sys
import subprocess
po_obj = subprocess.Popen("date", shell=True, stdout=subprocess.PIPE)
time.sleep(1)
content = po_obj.stdout.read()          # 读出子进程的输出
print(u"子进程的输出：", content)
```

运行结果如下：

$ python3 sp_popen_stdout_pipe.py
子进程的输出： b'Tue Jul  2 09:28:07 CST 2019\n'

> 类似地，也可以设置 stdin，这样就可以给子进程输入数据了。

下面介绍该类实例对象的相关接口。

#### 1) args：命令行字符串

这就是创建 Popen 时传入的参数。

```

>>> spp_obj = subprocess.Popen("date")
Tue Jul  2 07:54:22 CST 2019       # 子进程的标准输出内容
>>> spp_obj.args                   # Popen 对象的 args 属性
'date'
```

#### 2) wait（最大等待时间）：等待子进程结束

默认的等待时间表示永远，即一直等待直到子进程结束。

```

import time, os, sys, datetime
import subprocess
t_start = datetime.datetime.now()       # 进程开始的时间
print(u"启动子进程之前的时间：", t_start)
po_obj = subprocess.Popen("sleep 10", shell=True)
po_obj.wait()                           # 等待子进程结束
t_end = datetime.datetime.now()
print(u"子进程结束的时间：", t_end)
t_diff = t_end - t_start
print(u"子进程运行的时间：", t_diff)
```

运行结果如下：

$ python sp_popen_wait.py                       # 启动脚本
启动子进程之前的时间： 2019-07-02 09:12:47.915412 # 代码第 4 行输出
子进程结束的时间： 2019-07-02 09:12:57.920940     # 代码第 8 行输出
子进程运行的时间： 0:00:10.005528                 # 代码第 10 行输出

#### 3) kill()：杀死子进程

该函数在不同操作系统上的实现可能有所不同，但基本都是让进程停止运行。需要注意的是，在 kill() 之后还是需要调用 wait() 的，否则进程会一直存在，成为僵尸进程。下面的例子是在 macOS 系统上使用 kill() 接口的情形。

```

>>> import subprocess                  # 引入 subprocess 模块
>>> po_obj = subprocess.Popen("sleep 1000", shell=True)        # 启动进程
        # 由于子进程还没有结束，所以 returncode 为 None
>>> po_obj.returncode is None
True
>>> po_obj.pid                         # 得到进程 id，这个和操作系统的进程 id 一致
58497
>>> import os                          # 引入 os 模块
>>> os.system("ps 58497")              # 查看进程 58497 的情况
  PID   TT  STAT      TIME COMMAND
58497 s001  S+     0:00.00 sleep 1000  # S+表示处于休眠状态
0
>>> po_obj.kill()                      # 杀掉进程
>>> os.system("ps 58497")              # 查看进程状态
  PID   TT  STAT      TIME COMMAND
58497 s001  Z+     0:00.00 (sleep)     # Z+表示是僵尸状态
0
>>> r = po_obj.wait()                  # 处理僵尸进程
>>> os.system("ps 58497")              # 再次查看进程状态
  PID   TT  STAT      TIME COMMAND     # 已经不存在该进程了
256
>>> r                                  # 查看 wait()的返回值，就是进程的返回值
-9
>>> po_obj.returncode                  # 使另外一种方式查看进程返回值
-9
```

#### 4) pid：进程号

这是一个属性，表示进程号。该属性和操作系统的进程 ID 是一致的。

#### 5) returncode：返回值

这是一个属性，表示进程的返回值。该属性仅在进程结束之后有效，在进程运行过程中始终为 None。