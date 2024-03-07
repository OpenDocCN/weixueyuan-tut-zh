# Linux 网络通信工具

现在是一个互联网的时代，你不可避免的要和其他用户进行远程交流，连接到远程主机。

## ping 命令

ping 命令会向网络上的主机发送应答请求，根据响应信息可以判断远程主机是否可用。

ping 命令的语法：

```
$ping hostname or ip-address
```

如果网络畅通，很快就可以看到响应信息。

例如，检测是否可以连接到谷歌的主机：

```
$ping google.com
PING google.com (74.125.67.100) 56(84) bytes of data.
64 bytes from 74.125.67.100: icmp_seq=1 ttl=54 time=39.4 ms
64 bytes from 74.125.67.100: icmp_seq=2 ttl=54 time=39.9 ms
64 bytes from 74.125.67.100: icmp_seq=3 ttl=54 time=39.3 ms
64 bytes from 74.125.67.100: icmp_seq=4 ttl=54 time=39.1 ms
64 bytes from 74.125.67.100: icmp_seq=5 ttl=54 time=38.8 ms
--- google.com ping statistics ---
22 packets transmitted, 22 received, 0% packet loss, time 21017ms
rtt min/avg/max/mdev = 38.867/39.334/39.900/0.396 ms
$
```

如果主机没有响应，可以看到类似下面的信息：

```
$ping giiiiiigle.com
ping: unknown host giiiiigle.com
$
```

## ftp 工具

ftp 是 File Transfer Protocol 的缩写，称为文件传输协议。通过 ftp 工具，能够将文件上传到远程服务器，也可以从远程服务器下载文件。

ftp 工具有自己的命令（类似 Linux 命令），可以：

*   连接并登录远程主机；
*   查看目录，遍历目录下的文件；
*   上传或下载文件，包括文本文件、二进制文件等。

ftp 命令的用法如下：

```
$ftp hostname or ip-address
```

接下来会提示你输入用户名和密码，验证成功后会进入主目录，然后就可以使用 ftp 工具的命令进行操作了。

| ftp 命令 | 说明 |
| put filename | 将本地文件上传到远程主机。 |
| get filename | 将远程文件下载到本地。 |
| mput file list | 将多个本地文件上传到远程主机。 |
| mget file list | 将多个远程文件下载到本地。 |
| prompt off | 关闭提示。默认情况下，使用 mput 或 mget 命令会不断提示你确认文件的上传或下载。 |
| prompt on | 打开提示。 |
| dir | 列出远程主机当前目录下的所有文件。 |
| cd dirname | 改变远程主机目录。 |
| lcd dirname | 改变本地目录。 |
| quit | 退出登录。 |

注意，所有的上传和下载都是针对本地主机和远程主机的当前目录，如果你希望上传指定目录下的文件，首先要 cd 到该目录，然后才能上传。

ftp 工具使用举例：

```
$ftp amrood.com
Connected to amrood.com.
220 amrood.com FTP server (Ver 4.9 Thu Sep 2 20:35:07 CDT 2009)
Name (amrood.com:amrood): amrood
331 Password required for amrood.
Password:
230 User amrood logged in.
ftp> dir
200 PORT command successful.
150 Opening data connection for /bin/ls.
total 1464
drwxr-sr-x   3 amrood   group       1024 Mar 11 20:04 Mail
drwxr-sr-x   2 amrood   group       1536 Mar  3 18:07 Misc
drwxr-sr-x   5 amrood   group        512 Dec  7 10:59 OldStuff
drwxr-sr-x   2 amrood   group       1024 Mar 11 15:24 bin
drwxr-sr-x   5 amrood   group       3072 Mar 13 16:10 mpl
-rw-r--r--   1 amrood   group     209671 Mar 15 10:57 myfile.out
drwxr-sr-x   3 amrood   group        512 Jan  5 13:32 public
drwxr-sr-x   3 amrood   group        512 Feb 10 10:17 pvm3
226 Transfer complete.
ftp> cd mpl
250 CWD command successful.
ftp> dir
200 PORT command successful.
150 Opening data connection for /bin/ls.
total 7320
-rw-r--r--   1 amrood   group       1630 Aug  8 1994  dboard.f
-rw-r-----   1 amrood   group       4340 Jul 17 1994  vttest.c
-rwxr-xr-x   1 amrood   group     525574 Feb 15 11:52 wave_shift
-rw-r--r--   1 amrood   group       1648 Aug  5 1994  wide.list
-rwxr-xr-x   1 amrood   group       4019 Feb 14 16:26 fix.c
226 Transfer complete.
ftp> get wave_shift
200 PORT command successful.
150 Opening data connection for wave_shift (525574 bytes).
226 Transfer complete.
528454 bytes received in 1.296 seconds (398.1 Kbytes/s)
ftp> quit
221 Goodbye.
$
```

## telnet 工具

Telnet 工具可以让我们连接并登录到远程计算机。

一旦连接到了远程计算机，就可以在上面进行各种操作了，例如：

```
C:>telnet amrood.com
Trying...
Connected to amrood.com.
Escape character is '^]'.

login: amrood
amrood's Password:
*****************************************************
*                                                   *
*                                                   *
*            WELCOME TO AMROOD.COM                  *
*                                                   *
*                                                   *
*****************************************************

Last unsuccessful login: Fri Mar  3 12:01:09 IST 2009
Last login: Wed Mar  8 18:33:27 IST 2009 on pts/10

   {  do your work }

$ logout
Connection closed.
C:>
```

## finger 工具

finger 可以让我们查看本地主机或远程主机上的用户信息。有些系统为了安全会禁用 finger 命令。

例如，查看本机在线用户：

```
$ finger
Login     Name       Tty      Idle  Login Time   Office
amrood               pts/0          Jun 25 08:03 (62.61.164.115)
```

查看本机指定用户的信息：

```
$ finger amrood
Login: amrood                           Name: (null)
Directory: /home/amrood                 Shell: /bin/bash
On since Thu Jun 25 08:03 (MST) on pts/0 from 62.61.164.115
No mail.
No Plan.
```

查看远程主机上的在线用户：

```
$ finger @avtar.com
Login     Name       Tty      Idle  Login Time   Office
amrood               pts/0          Jun 25 08:03 (62.61.164.115)
```

查看远程主机上某个用户的信息：

```
$ finger amrood@avtar.com
Login: amrood                           Name: (null)
Directory: /home/amrood                 Shell: /bin/bash
On since Thu Jun 25 08:03 (MST) on pts/0 from 62.61.164.115
No mail.
No Plan.
```