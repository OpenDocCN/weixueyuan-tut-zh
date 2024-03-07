# Nginx 进程配置指令详解

> 原文：[`www.weixueyuan.net/a/626.html`](http://www.weixueyuan.net/a/626.html)

Nginx 的进程配置指令包含在 Nginx 核心代码及事件模块代码中，按配置指令设定的功能可分为进程管理、进程调优、进程调试、事件处理 4 个部分。

## 1、进程管理

Nginx 本身是一款应用软件，在其运行时，用户可对其运行方式、动态加载模块、日志输出等使用其内建的基础配置指令进行配置，指令说明如下表所示。

表：进程管理指令

| 指令 | 默认值 | 指令说明 |
| daemon | on | 用于设定 Nginx 进程是否以守护进程的方式在后台运行，on 为启用，off 为不启用 |
| pid | logs/nginx.pid | 设定保存 Nginx 主进程 ID 的文件路径 |
| user | nobody nobody | 用于设定 Nginx 启动后，主进程唤起的工作进程运行的用户及用户组 |
| load_module | -- | 加载动态模块的指令 |
| include | -- | 加载外部配置文件 |
| error_log | logs/error.log error | 指定错误日志文件路径及文件名 |
| pcre_jit | off | 用于设定在配置文件中的正则表达式是否使用 pcre_jit 技术，off 为不使用，on 为使用 |
| ssl_engine | -- | 指定使用的 OpenSSL 加速引擎名称 |

其中，pcre_jit 需要 Nginx 在配置编译时加上 --with-pcre-jit 参数；error_log 的日志级别可以为如下值：debug、info、notice、warn、error、crit、alert、emerg。

在 Linux 系统中，可用如下命令查看当前系统支持的 OpenSSL 加速引擎信息。

openssl engine -t

## 2、进程调优

Nginx 是按照事件驱动架构设计的。每个外部请求都以事件的形式被工作进程（Worker Process）响应，并发完成各种功能的操作处理。Nginx 工作进程的性能依赖于硬件和操作系统的配置，在实际应用场景中，用户需要按照硬件、操作系统或应用场景需求的侧重点进行相应的配置调整。

Nginx 的进程调优配置指令如下面表格中所示。

表：线程池指令

| 名 称 | 线程池指令 |
| 指令 | thread_pool |
| 作用 域 | main |
| 默认值 | thread_pool default threads=32 max_queue=65536; |
| 指令说明 | 线程池配置指令，允许调整默认线程池或创建新的线程池，用于读取和发送文件的场景中。在线程池中所有线程都繁忙时，新的请求任务将在队列中等待，默认情况下，等待队列中的最大任务数是 65536，使用线程池机制时，通过配置该指令，可以在因读取和发送文件引发阻塞的场景中提升 Nginx 读取和发送文件的处理性能 |

配置样例如下：

thread_pool pool_1 threads=16;

具体参数说明如下。

*   thread_pool 也可以编写在 http 指令域中；
*   threads 参数定义了线程池的线程数；
*   max_queue 参数指定了等待队列中的最大任务数，在线程池中所有线程都处于繁忙状态时，新任务将进入等待队列。等待队列中的最大任务数为 65536；
*   线程池指令需要在编译配置时增加 --with-threads 参数。

表：定时器方案指令

| 名 称 | 定时器方案指令 |
| 指令 | timer_resolution |
| 作用域 | main |
| 默认值 | -- |
| 指令说明 | Nginx 中的处理事件超时管理方案有两种，一种是设定一个定时器，每过一段时间就对所有超时事件进行一次扫描；另一种是先计算出距离当前时间最近的将要发生超时事件的时间，然后等待这个时间之后再去进行一次超时检测。默认配置下使用第二种超时检测方案，该方案是依据事件超时时间与当前时间的时间差进行检测的，所以每次事件返回都需要进行新的检测时间计算，在 I/O 事件比较多的场景下，这会导致频繁地调用时间函数 gettimeofday 进行计算并更新下次检测的时间，资源消耗相对较高。而设置一个指定的时间值启用第一种方案时，Nginx 内置的事件超时检测定时器会在指定时间周期内进行事件超时检测，无须调用时间函数 gettimeofday 更新时间，资源消耗相对较低 |

配置样例如下：

timer_resolution 100ms;

在因频繁调用时间函数引发的资源消耗不大的场景中可不设定该指令。

表：工作进程优先级指令

| 名 称 | 工作进程优先级指令 |
| 指令 | worker_priority |
| 作用域 | main |
| 默认值 | 0 |
| 指令说明 | 工作进程优先级设定指令，可以通过该指令设定工作进程在 Linux 系统中的优先级（nice 值） |

配置样例如下：

worker_priority -5;

worker_priority 指令值的取值范围是 -20～19，数值越小，优先级越高，获得的 CPU 时间就越多。配置生效后可以通过如下命令查看，输出结果如下图所示。

ps axo command,pid,ni | grep nginx | grep -v grep

![Nginx 工作进程](img/307416da83d71d342938e18a1f1b0d64.png)
图：Nginx 工作进程

表：工作进程数指令

| 名 称 |  工作进程数指令 |
| 指令 | worker_processes |
| 作用域 | main |
| 默认值 | 1 |
| 可配置选项 | number 或 auto |
| 指令说明 | 依据 Nginx 架构可知，工作进程数量的最佳配置是小于或等于 CPU 内核的数量。通过该指令可以手动设置工作进程的数量，该指令也支持 auto 指令值，由 Nginx 进行自动分配 |

配置样例如下：

worker_processes auto;

工作进程数指令的指令值有两种类型，分别为数字和 auto。指令值为 auto 时，Nginx 会根据 CPU 的内核数生成等数量的工作进程。

表：工作进程 CPU 绑定指令

| 名 称 | 工作进程 CPU 绑定指令 |
| 指令 | worker_cpu_affinity |
| 作用域 | main |
| 默认值 | -- |
| 可配置选项 | cpumark 或 auto |
| 指令说明 | Nginx 工作进程处于高效的工作状态是因为充分利用了进程与 CPU 的亲缘性，使每个工作进程均可固定在一个 CPU 上运行。该指令可以手动进行工作进程与 CPU 的绑定，当然也可以通过设定指令值 auto 交由 Nginx 自动分配 |

配置样例如下：

worker_processes 8;
worker_cpu_affinity 00000001 00000010 00000100 00001000 00010000 00100000 01000000 10000000;

指令值是用 CPU 掩码来表示的，使用与 CPU 数量相等位数的二进制值来表示。单个 CPU 用单个二进制值表示，多个 CPU 组合可用二进制值相加来表示。如配置样例所示，CPU 有 8 个核，分别表示绑定了从第 0 核到第 7 核的 CPU。CPU 核数是从 0 开始计数的。

指令值除了可以是 CPU 掩码外，还可以是 auto。当指令值为 auto 时，Nginx 会自动进行 CPU 绑定。

配置样例如下：

worker_processes auto; 
worker_cpu_affinity auto;

工作进程与 CPU 核数也可以是多种对应组合，指令语句如下：

worker_processes 4; 
worker_cpu_affinity 01 10 01 10; # 表示把第 1、3 工作进程绑定在 2 核 CPU 的第 0 核，第 2、4 工作
                                 # 进程绑定在 2 核 CPU 的第 1 核

worker_processes 2; 
worker_cpu_affinity 0101 1010;   # 表示把第 1 工作进程绑定在 CPU 的第 0 核和第 2 核，第 2 工作进程
                                 # 绑定在 CPU 的第 1 核和第 3 核

工作进程 CPU 绑定指令仅适合于 FreeBSD 和 Linux 操作系统。

表：工作进程开文件数指令

| 名 称  | 工作进程开文件数指令 |
| 指令 |  worker_rlimit_nofile |
| 作用域  |  main |
| 默认值 | -- |
| 指令说明 | 设置 Nginx 所有工作进程同时打开文件的最大数量，默认为操作系统的文件打开数 |

配置样例如下：

worker_rlimit_nofile 65535;

表：工作进程关闭等待时间指令

| 名 称 | 工作进程关闭等待时间指令 |
| 指令 | worker_shutdown_timeout |
| 作用域 | main |
| 默认值 | -- |
| 指令说明 | 设置 Nginx 正常关闭工作进程的超时时间，当超过设定的时间时，Nginx 主进程将强制关闭所有已经打开的连接，以便关闭工作进程 |

配置样例如下：

worker_shutdown_timeout 10s;

表：设置互斥锁文件指令

| 名 称 | 设置互斥锁文件指令 |
| 指令 | lock_file |
| 作用域 | main |
| 默认值 | logs/nginx.lock; |
| 指令说明 | 设置互斥锁文件指令，在开启 accept mutex 进程调度模式或使用共享内存的场景下，需要用到互斥锁机制。在一些支持原子操作的操作系统中，可使用共享内存实现互斥锁。在不支持原子操作的系统环境下，需要通过该指令指定一个互斥锁文件 |

配置样例如下：

lock_file logs/nginx.lock;

## 3、进程调试

Nginx 调整配置或运行发生异常时，为了及时获知工作进程在事件处理过程中发生的问题，可通过获取内存中各状态机、变量等数据的内容进行调试。Nginx 为用户提供了一些调试用的配置指令，方便用户进行进程调试。配置指令如下面表格中所示。

表：主进程指令

| 名 称 | 主进程指令 |
| 指令 | master_process |
| 作用域  | main |
| 默认值 | on |
| 可配置选项 | on 或 off |
| 指令说明  | Nginx 默认是以一个主进程管理多个工作进程的工作方式，设定指令值为 off 时，Nginx 将只运行一个主进程来处理所有请求 |

配置样例如下：

master_process off;

当只由主进程处理请求时，调试进程会更加方便。

表：调试点控制指令

| 名 称 | 调试点控制指令 |
| 指令 | debug_points |
| 作用域 | main |
| 默认值  | -- |
| 可配置选项 | stop 或 abort |
| 指令说明 | 该指令用于进行调试点的控制，当指令值为 stop 时，Nginx 在执行到内部调试点时就会发出 SIGSTOP 信号，方便用户进行调试；当指令值为 abort 时则会停止进程并创建 corefile |

配置样例如下：

debug_points stop;

表：工作目录指令

| 名称 | 工作目录指令 |
| 指令 | working_directory |
| 作用域 | main |
| 默认值 | -- |
| 指令说明 | 在 Linux 操作系统中，当进程执行出错或收到终止信号时，操作系统会将执行进程过程中，内存中的内容存储到一个文件中，该文件被称为崩溃文件（corefile），当 Nginx 进程发生这种状况时也会生成一个崩溃文件，该崩溃文件中包含当时的堆栈及寄存器等信息，方便用户排查问题产生的原因。该指令用于设定工作进程保存崩溃文件的目录，在 Nginx 程序崩溃时向该目录中写入崩溃文件，Nginx 进程需要被设定有目录的写权限 |

配置样例如下：

working_directory logs

可以使用工具 objdump、GDB 进行文件分析。

表：调试文件大小指令

| 名称 | 调试文件大小指令 |
| 指令 | worker_rlimit_core |
| 作用域 | main |
| 默认值 | -- |
| 指令说明 | 该指令是崩溃文件大小的设置指令。因为崩溃文件会存储非常详细的信息，数据量很大，很容易把磁盘空间占满，因此需要合理限制崩溃文件的文件大小 |

配置样例如下：

worker_rlimit_core 800m;

## 4、事件处理

Nginx 是采用事件驱动式架构处理外部请求的，这一架构使得 Nginx 在现有硬件架构下可以处理数以万计的并发请求。通过事件处理指令的配置可以让 Nginx 与实际运行的硬件及系统进行有效的适配，从而发挥更加高效的并发处理能力。Nginx 的事件处理指令编辑在 events 指令域中，如下面表格中所示。

表：工作进程并发数指令

| 名称 | 工作进程并发数指令 |
| 指令 | worker_connections |
| 作用域 | events |
| 默认值 | 512 |
| 指令说明 | 每个 Nginx 工作进程可处理并发连接的最大数 |

配置样例如下：

events {
    worker_connections 65535;
}

Linux 系统下，因为每个网络连接都将打开一个文件描述符，Nginx 可处理的并发连接数受限于操作系统的最大打开文件数，同时所有工作进程的并发数也受 worker_rlimit_nofile 指令值的限制。

表：事件处理机制选择指令

| 名 称 | 事件处理机制选择指令 |
| 指令 | use |
| 作用域 | events |
| 默认值 | -- |
| 指令说明 | Nginx 内部有多种事件处理机制模型，以下简称事件模型。默认情况下，Nginx 会自动选择一种高效的事件模型，用户可以通过该指令自行选择事件模型进行事件处理 |

配置样例如下：

events {
    use epoll;
}

Nginx 支持的事件模型有 select、poll、kqueue、epoll、/dev/poll、eventport。

表：互斥锁指令

| 名称 | 互斥锁指令 |
| 指令 | accept_mutex |
| 作用域  | events |
| 默认值 | off |
| 可配置选项  | on 或 off |
| 指令说明 | 设置是否启用互斥锁模式的进程调度 |

配置样例如下：

events {
    accept_mutex on;
}

在 Nginx 1.11.3 版本之前，互斥锁指令是默认开启的。

表：互斥锁等待时间指令

| 名称  | 互斥锁等待时间指令 |
| 指令 | accept_mutex_delay |
| 作用域 | events |
| 默认值 | 500ms |
| 指令说明 | Nginx 工作进程在互斥锁模式下需要不断地争抢互斥锁，没有互斥锁的工作进程如果争抢不到互斥锁，会在等待时间结束后执行下一轮争抢。通过该指令可以将抢锁等待时间设置为一个较短的时间，以提高进程争抢互斥锁的频率 |

配置样例如下：

events {
    accept_mutex_delay 300ms;
}

表：多请求支持指令

| 名称 |  多请求支持指令 |
| 指令 | multi_accept |
| 作用域 | events |
| 默认值 | off |
| 可配置选项 | on 或 off |
| 指令说明 | 默认情况下，每个工作进程一次只接收一个新连接。如果开启该指令，则每个工作进程将接收所有的新连接 |

配置样例如下：

events {
    multi_accept on;
}

表：未完成异步操作最大数指令

| 名称 | 未完成异步操作最大数指令 |
| 指令 | worker_aio_requests |
| 作用域 | events |
| 默认值 | 32 |
| 指令说明 | 用于设置当在 epoll 事件模型下使用 AIO 时，单个工作进程未完成异步 I/O 操作的最大数 |

配置样例如下：

events {
    worker_aio_requests 128;
}

表：调试指定连接指令

| 名称 | 调试指定连接指令 |
| 指令 | debug_connection |
| 作用域 | events |
| 默认值 | off |
| 可配置选项 | address 或 CIDR 或 unix: |
| 指令说明 |  对指定的客户端连接开启调试日志 |

配置样例如下：

events {
    debug_connection 127.0.0.1;
    debug_connection localhost;
    debug_connection 192.0.2.0/24;
    debug_connection ::1;
    debug_connection 2001:0db8::/32;
    debug_connection unix:;
    ...
}

该指令需要 Nginx 在编译时通过 `--with-debug` 参数开启。

## 5、指令配置样例

本节指令的配置样例如下。

daemon on;                                              # 以守护进程的方式运行 Nginx
pid  logs/nginx.pid;                                    # 主进程 ID 记录在 logs/nginx.pid 中
user nobody nobody;                                     # 工作进程运行用户为 nobody
load_module "modules/ngx_http_xslt_filter_module.so";   # 加载动态模块 ngx_http_xslt_
                                                        # filter_module.so
error_log  logs/error.log debug;                        # 错误日志输出级别为 debug
pcre_jit on;                                            # 启用 pcre_jit 技术
thread_pool default threads=32 max_queue=65536;         # 线程池的线程数为 32，等待队列中的最大
                                                          # 任务数为 65536
timer_resolution 100ms;                                 # 定时器周期为 100 毫秒
worker_priority -5;                                     # 工作进程系统优先级为-5
worker_processes auto;                                  # 工作进程数由 Nginx 自动调整
worker_cpu_affinity auto;                               # 工作进程的 CPU 绑定由 Nginx 自动调整
worker_rlimit_nofile 65535;                             # 所有工作进程的最大连接数是 65535
worker_shutdown_timeout 10s;                            # 工作进程关闭等待时间是 10 秒
lock_file logs/nginx.lock;                              # 互斥锁文件的位置是 logs/nginx.lock

working_directory logs                                  # 工作进程工作目录是 logs
debug_points stop;                                      # 调试点模式为 stop
worker_rlimit_core 800m;                                # 崩溃文件大小为 800MB

events {
    worker_connections 65535;                           # 每个工作进程的最大连接数是 65535
    use epoll;                                          # 指定事件模型为 epoll
    accept_mutex on;                                    # 启用互斥锁模式的进程调度
    accept_mutex_delay 300ms;                           # 互斥锁模式下进程等待时间为 300 毫秒
    multi_accept on;                                    # 启用支持多连接
    worker_aio_requests 128;                            # 完成异步操作最大数为 128
    debug_connection 192.0.2.0/24;                       # 调试指定连接的 IP 地址和端口是 192.0.2.0/24
}