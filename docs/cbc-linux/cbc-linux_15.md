# Linux 系统日志及日志分析

Linux 系统拥有非常灵活和强大的日志功能，可以保存几乎所有的操作记录，并可以从中检索出我们需要的信息。

大部分 Linux 发行版默认的日志守护进程为 syslog，位于 /etc/syslog 或 /etc/syslogd，默认配置文件为 /etc/syslog.conf，任何希望生成日志的程序都可以向 syslog 发送信息。 

Linux 系统内核和许多程序会产生各种错误信息、警告信息和其他的提示信息，这些信息对管理员了解系统的运行状态是非常有用的，所以应该把它们写到日志文件中去。完成这个过程的程序就是 syslog。syslog 可以根据日志的类别和优先级将日志保存到不同的文件中。例如，为了方便查阅，可以把内核信息与其他信息分开，单独保存到一个独立的日志文件中。默认配置下，日志文件通常都保存在“/var/log”目录下。

### 日志类型

下面是常见的日志类型，但并不是所有的 Linux 发行版都包含这些类型：

| 类型 | 说明 |
| auth | 用户认证时产生的日志，如 login 命令、su 命令。 |
| authpriv | 与 auth 类似，但是只能被特定用户查看。 |
| console | 针对系统控制台的消息。 |
| cron | 系统定期执行计划任务时产生的日志。 |
| daemon | 某些守护进程产生的日志。 |
| ftp | FTP 服务。 |
| kern | 系统内核消息。 |
| local0.local7 | 由自定义程序使用。 |
| lpr | 与打印机活动有关。 |
| mail | 邮件日志。 |
| mark | 产生时间戳。系统每隔一段时间向日志文件中输出当前时间，每行的格式类似于 May 26 11:17:09 rs2 -- MARK --，可以由此推断系统发生故障的大概时间。 |
| news | 网络新闻传输协议(nntp)产生的消息。 |
| ntp | 网络时间协议(ntp)产生的消息。 |
| user | 用户进程。 |
| uucp | UUCP 子系统。 |

### 日志优先级

常见的日志优先级请见下标：

| 优先级 | 说明 |
| emerg | 紧急情况，系统不可用（例如系统崩溃），一般会通知所有用户。 |
| alert | 需要立即修复，例如系统数据库损坏。 |
| crit | 危险情况，例如硬盘错误，可能会阻碍程序的部分功能。 |
| err | 一般错误消息。 |
| warning | 警告。 |
| notice | 不是错误，但是可能需要处理。 |
| info | 通用性消息，一般用来提供有用信息。 |
| debug | 调试程序产生的信息。 |
| none | 没有优先级，不记录任何日志消息。 |

### 常见日志文件

所有的系统应用都会在 /var/log 目录下创建日志文件，或创建子目录再创建日志文件。例如：

| 文件/目录 | 说明 |
| /var/log/boot.log | 开启或重启日志。 |
| /var/log/cron | 计划任务日志 |
| /var/log/maillog | 邮件日志。 |
| /var/log/messages | 该日志文件是许多进程日志文件的汇总，从该文件可以看出任何入侵企图或成功的入侵。 |
| /var/log/httpd 目录 | Apache HTTP 服务日志。 |
| /var/log/samba 目录 | samba 软件日志 |

### /etc/syslog.conf 文件

/etc/syslog.conf 是 syslog 的配置文件，会根据日志类型和优先级来决定将日志保存到何处。典型的 syslog.conf 文件格式如下所示：

```
*.err;kern.debug;auth.notice /dev/console
daemon,auth.notice           /var/log/messages
lpr.info                     /var/log/lpr.log
mail.*                       /var/log/mail.log
ftp.*                        /var/log/ftp.log
auth.*                       @see.xidian.edu.cn
auth.*                       root,amrood
netinfo.err                  /var/log/netinfo.log
install.*                    /var/log/install.log
*.emerg                      *
*.alert                      |program_name
mark.*                       /dev/console
```

第一列为日志类型和日志优先级的组合，每个类型和优先级的组合称为一个选择器；后面一列为保存日志的文件、服务器，或输出日志的终端。syslog 进程根据选择器决定如何操作日志。

对配置文件的几点说明：

*   日志类型和优先级由点号(.)分开，例如 kern.debug 表示由内核产生的调试信息。
*   kern.debug 的优先级大于 debug。
*   星号(*)表示所有，例如 *.debug 表示所有类型的调试信息，kern.* 表示由内核产生的所有消息。
*   可以使用逗号(,)分隔多个日志类型，使用分号(;)分隔多个选择器。

对日志的操作包括：

*   将日志输出到文件，例如 /var/log/maillog 或 /dev/console。
*   将消息发送给用户，多个用户用逗号(,)分隔，例如 root, amrood。
*   通过管道将消息发送给用户程序，注意程序要放在管道符(|)后面。
*   将消息发送给其他主机上的 syslog 进程，这时 /etc/syslog.conf 文件后面一列为以@开头的主机名，例如@see.xidian.edu.cn。

### logger 命令

logger 是 Shell 命令，可以通过该命令使用 syslog 的系统日志模块，还可以从命令行直接向系统日志文件写入一行信息。

logger 命令的语法为：

```
logger [-i] [-f filename] [-p priority] [-t tag] [message...]
```

每个选项的含义如下：

| 选项 | 说明 |
| -f filename | 将 filename 文件的内容作为日志。 |
| -i | 每行都记录 logger 进程的 ID。 |
| -p priority | 指定优先级；优先级必须是形如 facility.priority 的完整的选择器，默认优先级为 user.notice。 |
| -t tag | 使用指定的标签标记每一个记录行。 |
| message | 要写入的日志内容，多条日志以空格为分隔；如果没有指定日志内容，并且 -f filename 选项为空，那么会把标准输入作为日志内容。 |

例如，将 ping 命令的结果写入日志：

```
$ ping 192.168.0.1 | logger -it logger_test -p local3.notice&
$ tail -f /var/log/userlog
Oct 6 12:48:43 kevein logger_test[22484]: PING 192.168.0.1 (192.168.0.1) 56(84) bytes of data.
Oct 6 12:48:43 kevein logger_test[22484]: 64 bytes from 192.168.0.1: icmp_seq=1 ttl=253 time=49.7 ms
Oct 6 12:48:44 kevein logger_test[22484]: 64 bytes from 192.168.0.1: icmp_seq=2 ttl=253 time=68.4 ms
Oct 6 12:48:45 kevein logger_test[22484]: 64 bytes from 192.168.0.1: icmp_seq=3 ttl=253 time=315 ms
Oct 6 12:48:46 kevein logger_test[22484]: 64 bytes from 192.168.0.1: icmp_seq=4 ttl=253 time=279 ms
Oct 6 12:48:47 kevein logger_test[22484]: 64 bytes from 192.168.0.1: icmp_seq=5 ttl=253 time=347 ms
Oct 6 12:48:49 kevein logger_test[22484]: 64 bytes from 192.168.0.1: icmp_seq=6 ttl=253 time=701 ms
Oct 6 12:48:50 kevein logger_test[22484]: 64 bytes from 192.168.0.1: icmp_seq=7 ttl=253 time=591 ms
Oct 6 12:48:51 kevein logger_test[22484]: 64 bytes from 192.168.0.1: icmp_seq=8 ttl=253 time=592 ms
Oct 6 12:48:52 kevein logger_test[22484]: 64 bytes from 192.168.0.1: icmp_seq=9 ttl=253 time=611 ms
Oct 6 12:48:53 kevein logger_test[22484]: 64 bytes from 192.168.0.1: icmp_seq=10 ttl=253 time=931 ms
```

ping 命令的结果成功输出到 /var/log/userlog 文件。

命令 logger -it logger_test -p local3.notice 各选项的含义：

*   -i：在每行都记录进程 ID；
*   -t logger_test：每行记录都加上“logger_test”这个标签；
*   -p local3.notice：设置日志类型和优先级。

### 日志转储

日志转储也叫日志回卷或日志轮转。Linux 中的日志通常增长很快，会占用大量硬盘空间，需要在日志文件达到指定大小时分开存储。

syslog 只负责接收日志并保存到相应的文件，但不会对日志文件进行管理，因此经常会造成日志文件过大，尤其是 WEB 服务器，轻易就能超过 1G，给检索带来困难。

大多数 Linux 发行版使用 logrotate 或 newsyslog 对日志进行管理。logrotate 程序不但可以压缩日志文件，减少存储空间，还可以将日志发送到指定 E-mail，方便管理员及时查看日志。

例如，规定邮件日志 /var/log/maillog 超过 1G 时转储，每周一次，那么每隔一周 logrotate 进程就会检查 /var/log/maillog 文件的大小：

*   如果没有超过 1G，不进行任何操作。
*   如果在 1G~2G 之间，就会创建新文件 /var/log/maillog.1，并将多出的 1G 日志转移到该文件，以给 /var/log/maillog 文件瘦身。
*   如果在 2G~3G 之间，会继续创建新文件 /var/log/maillog.2，并将 /var/log/maillog.1 的内容转移到该文件，将 /var/log/maillog 的内容转移到 /var/log/maillog.1，以保持 /var/log/maillog 文件不超过 1G。

可以看到，每次转存都会创建一个新文件（如果不存在），命名格式为日志文件名加一个数字（从 1 开始自动增长），以保持当前日志文件和转存后的日志文件不超过指定大小。

logrotate 的主要配置文件是 /etc/logrotate.conf，/etc/logrotate.d 目录是对 /etc/logrotate.conf 的补充，或者说为了不使 /etc/logrotate.conf 过大而设置。

可以通过 cat 命令查看它的内容：

```
$cat /etc/logrotate.conf
# see "man logrotate" for details  //可以查看帮助文档
# rotate log files weekly
weekly                             //设置每周转储一次
# keep 4 weeks worth of backlogs
rotate 4                           //最多转储 4 次
# create new (empty) log files after rotating old ones
create                             //当转储后文件不存储时创建它
# uncomment this if you want your log files compressed
#compress                          //以压缩方式转储
# RPM packages drop log rotation information into this directory
include /etc/logrotate.d           //其他日志文件的转储方式，包含在该目录下
# no packages own wtmp -- we'll rotate them here
/var/log/wtmp {                    //设置/var/log/wtmp 日志文件的转储参数
    monthly                        //每月转储
    create 0664 root utmp          //转储后文件不存在时创建它，文件所有者为 root，所属组为 utmp，对应的权限为 0664
    rotate 1                       //最多转储一次
}
```

注意：include 允许管理员把多个分散的文件集中到一个，类似于 C 语言的 #include，将其他文件的内容包含进当前文件。

include 非常有用，一些程序会把转储日志的配置文件放在 /etc/logrotate.d 目录，这些配置文件会覆盖或增加 /etc/logrotate.conf 的配置项，如果没有指定相关配置，那么采用 /etc/logrotate.conf 的默认配置。

所以，建议将 /etc/logrotate.conf 作为默认配置文件，第三方程序在 /etc/logrotate.d 目录下自定义配置文件。

logrotate 也可以作为命令直接运行来修改配置文件。