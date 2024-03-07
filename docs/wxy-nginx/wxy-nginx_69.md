# Nginx Logrotate：日志归档

> 原文：[`www.weixueyuan.net/a/801.html`](http://www.weixueyuan.net/a/801.html)

Nginx 日志存储为文件时，同一 access_log 指令设置的日志文件是以单文件形式存储的，在日常使用中为方便维护，通常需要将日志文件按日期进行归档。虽然 Nginx 本身并没有这一功能，但实现日志归档的方法仍有很多，此处推荐使用 Logrotate 实现日志归档管理。

Logrotate 是 CentOS 操作系统内置日志管理工具，该工具可对系统中生成的大量日志文件进行归档管理，其允许对日志文件实行压缩、删除或邮寄等操作。Logrotate 可以按照每天、周、月或达到某一大小的日志文件进行归档操作，Logrotate 基于 anacrontab 实现计划任务，只需在 /etc/logrotate.d 目录下编写相关日志管理配置文件，就可以无须人工干预使用自动化方式完成日志归档操作。

#### 1) Logrotate 安装

yum -y install logrotate

#### 2) Logrotate 文件目录

```

/etc/logrotate.conf                     # logrotate 主配置文件
/usr/sbin/logrotate                     # logrotate 二进制文件
/etc/logrotate.d/                       # 自定义 logrotate 配置文件
/var/lib/logrotate/logrotate.status     # logrotate 管理日志执行记录的状态文件
```

#### 3) Logrotate 命令参数

```

    -d, --debug                             # 测试归档配置文件
    -f, --force                             # 立即执行归档操作
    -m, --mail=command                      # 指定发送邮件的命令（默认为'/bin/mail')
    -s, --state=statefile                   # 设置 logrotate.status 文件路径，可用于区分在同
                                                # 一系统下以不同用户身份运行的 logrotate 任务
    -v, --verbose                           # 显示配置详细信息
    -l, --log=STRING                        # 将 Logrotate 执行的详情输出到指定的文件

logrotate -v /etc/logrotate.conf                # 显示配置文件详细信息
logrotate -d /etc/logrotate.d/syslog -l /var/log/logrotate.log  # 配置文件，执行测试
logrotate -f /etc/logrotate.d/syslog    # 立即执行当前配置文件
```

#### 4) Logrotate 配置指令

Logrotate 配置指令如下表所示。

| 指令 | 指令说明 |
| 归档执行周期 |
| hourly | 日志归档周期为 1 小时，默认 Logrotate 的最小周期为 1 天，需额外调整该参数才可生效 |
| daily | 日志归档周期为 1 天 |
| weekly | 日志归档周期为 1 周 |
| monthly | 日志归档周期为 1 月，通常为每月的第一天 |
| 归档执行条件 |
| include | 读取外部参数文件 |
| missingok | 如果日志文件不存在，则不显示错误信息 |
| nomissingok | 如果日志文件不存在，则显示错误信息。默认配置 |
| size | 日志文件可被归档的最小值 |
| minsize | 日志文件可被归档的最小值，没到归档周期执行时间，不会执行归档操作 |
| maxsize | 日志文件超过设定值时，即使没到归档周期执行时间，也会执行归档操作 |
| ifempty | 即使日志文件为空，也执行归档操作 |
| notifempty | 如果日志文件为空，则不进行归档。默认设置 |
| tabooext | 不对设置扩展名的日志文件执行归档操作 |
| 归档文件命名 |
| start count | 使用日志文件归档次数作为归档文件扩展名，count 默认值为 1，默认配置 |
| dateext | 为归档文件名添加日期，默认追加到扩展名后 |
| dateformat | 设置归档文件名中的日期格式，使用“%Y%m%d%H”作为说明符，默认为-%Y%m%d |
| dateyesterday | 使用前一天的日期而非创建归档文件时的日期作为归档文件的文件名中的日期 |
| extension | 指定日志的扩展名，并将其设置为归档文件的扩展名，启用压缩时，压缩的扩展名在最后 |
| compressext | 启用压缩时，自定义归档文件扩展名，如将“.gz”改为“.ddd” |
| 归档文件保存方式 |
| compress | 对归档文件启用压缩，默认为 gzip 压缩 |
| nocompress | 不压缩归档文件。默认设置 |
| compresscmd | 指定压缩归档文件的命令，默认为 gzip 压缩 |
| uncompresscmd | 指定解压归档文件的命令，默认为 gunzip 解压 |
| compressoptions | 启用压缩时，设置压缩工具的命令选项 |
| delaycompress | 在下一个归档周期再对当前归档文件进行压缩 |
| nodelaycompress | 不延迟压缩。默认设置 |
| 归档执行方式 |
| copy | 为日志文件复制一个副本后再进行归档 |
| nocopy | 不复制源日志文件。默认配置 |
| copytruncate | 复制日志文件后清空日志文件的内容 |
| nocopytruncate | 复制源日志文件后，不清空源文件。默认设置 |
| create mode owner group, create owner group | 重命名日志文件，创建与日志文件同名的文件，默认 mode=0644 uid=0 gid=0，与 copy 指令不能同时使用 |
| nocreate | 不创建与日志文件同名的文件。默认设置 |
| olddir | 设置归档文件保存目录 |
| noolddir | 归档文件与源文件在同一目录。默认设置 |
| createolddir mode owner group | 如果 olddir 参数指定的目录不存在，则创建目录并指定属组，默认 mode = 0777 uid = 0 gid = 0 |
| nocreateolddir | 当 olddir 参数设定目录不存在时，不创建目录。默认设置 |
| prerotate ... endscript | 归档执行之前执行脚本，日志文件名为传入的第一个参数 |
| postrotate ... endscript | 归档执行之后执行脚本，日志文件名为传入的第一个参数 |
| firstaction ... endscript | prerotate 脚本之前，仅当第一个日志文件被开始执行归档操作时才执行脚本，日志文件名为传入的第一个参数 |
| lastaction ... endscript | postrotate 脚本之后，仅当最后一个日志文件执行归档操作结束时才执行脚本，日志文件名为传入的第一个参数 |
| preremove ... endscript | 删除日志文件之前执行脚本，日志文件名为传入的第一个参数 |
| sharedscripts | 当匹配的日志文件为多个时，prerotate 和 postrotate 脚本会在每个日志文件执行归档操作时都执行一次，启用共享模式会让 prerotate 和 postrotate 脚本在全局只运行一次 |
| nosharedscripts | 当匹配的日志文件为多个时，prerotate 和 postrotate 脚本会在每个日志文件执行归档操作时都执行一次。默认设置 |
| su user group | 指定操作源文件执行归档操作的用户及属组 |
| 归档文件清理 |
| mail | 设置接收归档文件的邮件地址 |
| nomail | 不将归档文件发送到任何邮件地址 |
| mailfirst | 将刚生成的归档文件发送到设置的邮箱 |
| maillast | 将要超过 maxage 设置时间的归档文件发送到设置的邮箱 |
| mахage | 设置过期归档文件的天数 |
| rotate | 保留归档文件数，默认为 0 |
| shred | 彻底删除 |
| shredcycles count | 彻底删除时，覆盖文件的次数，默认为 3 |
| noshred | 不彻底删除 |

关于上表有以下几点需要说明：

*   copy 与 create 是两种互斥的归档执行方式；
*   copy 方式是将日志文件复制一份后清空原日志文件的内容，并对复制的文件进行归档操作，应用程序继续向原日志文件输出日志。因日志文件复制与清空操作存在时间间隔，所以切割操作会因日志量的大小及实时产生的频率存在丢失的情况；
*   create 方式是将日志文件重命名，因日志文件的 inode 编号不变，应用程序会向新命名的文件输出日志。Logrotate 新创建原日志文件名的文件后执行重启或以信号机制通知应用程序重新向新日志文件输出日志内容，完成切割操作；
*   当与同一自定义配置匹配的日志文件为多个时，会并发执行归档操作。

#### 5) Logrotate 管理 Nginx 日志

根据 Logrotate 的功能特点，建议选择 create 方式进行日志归档管理，配置样例如下：

```

vi /etc/logrotate.d/nginx
/usr/local/nginx/logs/*.log {
    daily                                   # 日志归档周期为 1 天
    size 1                                  # 日志文件最小为 1 字节时才执行归档
    minsize 1                               # 日志文件最小为 1 字节时才执行归档
    notifempty                              # 日志文件不为空时才执行归档
    dateext                                 # 归档文件名添加时间字符串
    dateformat -%Y%m%d%H                    # 归档文件名时间字符串格式为-%Y%m%d%H
    dateyesterday                           # 归档文件名时间字符串以归档操作的前一天为时间戳
    extension .log                          # 归档文件名中保留日志的扩展名
    compress                                # 归档文件执行压缩
    delaycompress                           # 在归档执行的下个周期再进行压缩
    create                                  # 以创建新文件方式实现日志归档
    olddir /data/backup/nginx_logs          # 归档文件存储目录
    createolddir                            # 归档文件存储目录不存在时自动创建
    postrotate                              # 归档执行后执行脚本
        /usr/local/nginx/sbin/nginx -s reopen -g "pid /run/nginx.pid;"
                                                # 通知 Nginx 重新打开日志文件
    endscript
    sharedscripts                           # 启用脚本共享模式
    maxage 7                                # 归档文件最多保留 7 天
    rotate 7                                # 归档文件最多保留 7 份
}
```