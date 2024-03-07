# Nginx 配置简述

> 原文：[`www.weixueyuan.net/a/613.html`](http://www.weixueyuan.net/a/613.html)

前面我们已经成功的将 Nginx 安装到了我们的系统中，接下来还需要将 Nginx 简单的配置一下。

## 1、环境配置

Nginx 编译安装成功后，为了便于操作维护，建议把 Nginx 执行文件的路径添加到环境变量中，可以通过如下命令完成。

cat >/etc/profile.d/nginx.sh << EOF
PATH=$PATH:/usr/local/nginx/sbin
EOF
source /etc/profile

对于 OpenResty，为了保持与 Nginx 的维护一致性，可以将 Nginx 目录软连接到 /usr/local 目录下。

ln -s /usr/local/openresty/nginx /usr/local/nginx

在 CentOS 操作系统中，配置文件通常放在 /etc 目录下，建议将 Nginx 的 conf 目录软连接到 /etc 目录下。

ln -s /usr/local/nginx/conf /etc/nginx

## 2、命令行参数

Nginx 执行文件的命令行参数可以通过 -h 参数获取，Nginx 命令行参数如下：

Usage: nginx [-?hvVtTq] [-s signal] [-c filename] [-p prefix] [-g directives]

Options:
-?,-h         : this help
-v            : show version and exit
-V            : show version and configure options then exit
-t            : test configuration and exit
-T            : test configuration, dump it and exit
-q            : suppress non-error messages during configuration testing
-s signal     : send signal to a master process: stop, quit, reopen, reload
-p prefix     : set prefix path (default: /usr/local/openresty/nginx/)
-c filename   : set configuration file (default: conf/nginx.conf)
-g directives : set global directives out of configuration file

上述代码中的主要参数解释说明如下。

*   -v 参数：显示 Nginx 执行文件的版本信息；
*   -V 参数：显示 Nginx 执行文件的版本信息和编译配置参数；
*   -t 参数：进行配置文件语法检查，测试配置文件的有效性；
*   -T 参数：进行配置文件语法检查，测试配置文件的有效性，同时输出所有有效配置内容；
*   -q 参数：在测试配置文件有效性时，不输出非错误信息；
*   -s 参数：发送信号给 Nginx 主进程，信号可以为以下 4 个；
*   stop：快速关闭；
*   quit：正常关闭；
*   reopen：重新打开日志文件；
*   reload：重新加载配置文件，启动一个加载新配置文件的 Worker Process，正常关闭一个加载旧配置文件的 Worker Process；
*   -p 参数：指定 Nginx 的执行目录，默认为 configure 时的安装目录，通常为 /usr/local/nginx；
*   -c 参数：指定 nginx.conf 文件的位置，默认为 conf/nginx.conf；
*   -g 参数：外部指定配置文件中的全局指令。

应用示例如下：

nginx -t                                               # 执行配置文件检测
nginx -t -q                                          # 执行配置文件检测，且只输出错误信息
nginx -s stop                                      # 快速停止 Nginx
nginx -s quit                                       # 正常关闭 Nginx
nginx -s reopen                                  # 重新打开日志文件
nginx -s reload                                   # 重新加载配置文件
nginx -p /usr/local/newnginx            # 指定 Nginx 的执行目录
nginx -c /etc/nginx/nginx.conf          # 指定 nginx.conf 文件的位置
# 外部指定 pid 和 worker_processes 配置指令参数
nginx -g "pid /var/run/nginx.pid; worker_processes 'sysctl -n hw.ncpu';"

Tengine 的扩展命令如下：

nginx -m                                 # 列出所有的编译模块
nginx -l                                   # 列出支持的所有指令

## 3、注册系统服务

CentOS 系统环境中使用 systemd 进行系统和服务管理，可以按需守护进程，并通过 systemctl 命令进行 systemd 的监测和控制。为了方便 Nginx 应用进程的维护和管理，此处把 Nginx 注册成系统服务，由 systemd 进行服务管理，命令如下。

cat >/usr/lib/systemd/system/nginx.service <<EOF
[Unit]                                                                                     # 记录 service 文件的通用信息
Description=The Nginx HTTP and reverse proxy server      # Nginx 服务描述信息
After=network.target remote-fs.target nss-lookup.target  # Nginx 服务启动依赖，在指定服务之后启动

[Service]                                                                                        # 记录 service 文件的 service 信息
Type=forking                                    　                                         # 标准 UNIX Daemon 使用的启动方式
PIDFile=/run/nginx.pid                                                                 # Nginx 服务的 pid 文件位置
ExecStartPre=/usr/bin/rm -f /run/nginx.pid                                 # Nginx 服务启动前删除旧的 pid 文件
ExecStartPre=/usr/local/nginx/sbin/nginx -t -q                           # Nginx 服务启动前执行配置文件检测
ExecStart=/usr/local/nginx/sbin/nginx -g "pid /run/nginx.pid;"  # 启动 Nginx 服务
ExecReload=/usr/local/nginx/sbin/nginx -t -q                             # Nginx 服务重启前执行配置文件检测
ExecReload=/usr/local/nginx/sbin/nginx -s reload -g "pid /run/nginx.pid;" 
                                                                                                      # 重启 Nginx 服务
ExecStop=/bin/kill -s HUP $MAINPID                                          # 关闭 Nginx 服务
KillSignal=SIGQUIT
TimeoutStopSec=5
KillMode=process
PrivateTmp=true

[Install]                                                           # 记录 service 文件的安装信息
WantedBy=multi-user.target                        # 多用户环境下启用
EOF

systemctl enable nginx                            # 将 Nginx 服务注册为系统启动后自动启动
systemctl start nginx                               # 启动 Nginx 服务命令
systemctl reload nginx                            # reload Nginx 服务命令
systemctl stop nginx                               # stop Nginx 服务命令
systemctl status nginx                            # 查看 Nginx 服务运行状态命令