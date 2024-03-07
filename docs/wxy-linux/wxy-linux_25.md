# Linux ftp 命令：使用 ftp 协议上传和下载文件

> 原文：[`www.weixueyuan.net/a/354.html`](http://www.weixueyuan.net/a/354.html)

Linux ftp 命令允许用户利用 ftp 协议上传和下载文件，其使用方法说明如下：

ftp [选项] [主机名/IP]

ftp 的相关命令包括使用命令和内部命令，其中使用命令的格式如上所列，主要用于登录 ftp 服务器的过程中。内部命令是指成功登录后进行的一系列操作，下面会详细列出。若用户默认“主机名/ IP”，则可在转入到 ftp 内部命令后继续选择登录，ftp 命令选项说明如表 2 所示。

表 2：ftp 命令选项说明

| 选项 | 说明 |
| --- | --- |
| -v | 显示远程服务器的所有响应信息 |
| -n | 限制 ftp 的自动登录 |
| -d | 使用调试方式 |
| -g | 取消全局文件名 |

ftp 常见内部命令选项说明如表 3 所示。

表 3：ftp 常见内部命令选项说明

| 选项 | 说明 |
| --- | --- |
| account[password] | 提供成功登录远程系统后访问系统资源所需的补充口令 |
| ascii | 使用 ASCII 类型传输方式，为默认传输模式 |
| bin/type binary | 使用二进制文件传输方式（嵌入式开发中的常见方式） |
| bye | 退出 ftp 会话过程 |
| cd remote-dir | 进入远程主机目录 |
| cdup | 进入远程主机目录的父目录 |
| chmod mode file-name | 将远程主机文件 file-name 的存取方式设置为 mode |
| close | 中断与远程服务器的 ftp 会话（与 open 对应） |
| delete remote-file | 删除远程主机文件 |
| debug[debug-value] | 设置调试方式，显示发送至远程主机的每条命令 |
| dir/ls[remote-dir][local-file] | 显示远程主机目录，并将结果存入本地文件 local-file |
| disconnection | 同 close |
| get remote-file[local-file] | 将远程主机的文件 remote-file 传至本地硬盘的 local-file |
| lcd[dir] | 将本地工作目录切换至 dir |
| mdelete[remote-file] | 删除远程主机文件 |
| mget remote-files | 传输多个远程文件 |
| mkdir dir-name | 在远程主机中建一目录 |
| mput local-file | 将多个文件传输至远程主机 |
| open host[port] | 建立指定 ftp 服务器连接，可指定连接端口 |
| passive | 进入被动传输方式（在这种模式下，数据连接是由客户端程序发起的） |
| put local-file[remote-file] | 将本地文件 local-file 传送至远程主机 |
| reget remote-file[local-file] | 类似于 get，但若 local-file 存在，则从上次传输中断处续传 |
| size file-name | 显示远程主机文件的大小 |
| system | 显示远程主机的操作系统类型 |

例如，使用 ftp 命令访问“ftp：//ftp.kernel.org”站点，可以使用命令：

# ftp ftp.kernel.org

注意：若需要匿名登录，则在`Name（**.**.**.**）：`处输入`anonymous`，在`Password：`处输入自己的 E-mail 地址。若要传送二进制文件，务必要把模式改为 bin。