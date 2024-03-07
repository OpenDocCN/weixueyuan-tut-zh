# 下载 MySQL 的安装文件

## 下载 MySQL 的安装文件

安装 MySQL 需要下面两个文件：
　　MySQL-server-4.0.16-0.i386.rpm　　　
　　MySQL-client-4.0.16-0.i386.rpm

下载地址为：http://www.mysql.com/downloads/mysql-4.0.html， 打开此网页，下拉网页找到“Linux x86 RPM downloads”项，找到“Server”和“Client programs”项，下载需要的上述两个 rpm 文件。

rpm 文件是 Red Hat 公司开发的软件安装包，rpm 可让 Linux 在安装软件包时免除许多复杂的手续。该命令在安装时常用的参数是 –ivh，其中 i 表示将安装指定的 rmp 软件包，V 表示安装时的详细信息，h 表示在安装期间出现“#”符号来显示目前的安装过程。这个符号将持续到安装完成后才停止。

## 安装服务器端

在有两个 rmp 文件的目录下运行如下命令：
　　[root@test1 local]# rpm -ivh MySQL-server-4.0.16-0.i386.rpm
显示如下信息。
　　warning: MySQL-server-4.0.16-0.i386.rpm: V3 DSA signature: NOKEY, key ID 5072e1f5
　　Preparing...　　　　　　　########################################### [100%]
　　1:MySQL-server　　　　　########################################### [100%]
　　　。。。。。。（省略显示）
　　/usr/bin/mysqladmin -u root password 'new-password'
　　/usr/bin/mysqladmin -u root -h test1 password 'new-password'
　　　。。。。。。（省略显示）
　　Starting mysqld daemon with databases from /var/lib/mysql

如出现如上信息，服务端安装完毕。测试是否成功可运行 netstat 看 Mysql 端口是否打开，如打开表示服务已经启动，安装成功。Mysql 默认的端口是 3306。
　　[root@test1 local]# netstat -nat
　　Active Internet connections (servers and established)
　　Proto Recv-Q Send-Q Local Address　　　　　 Foreign Address　　　　 State　　　
　　tcp　　0　　0 0.0.0.0:3306　　　　 0.0.0.0:*　　　　　 LISTEN　　　
上面显示可以看出 MySQL 服务已经启动。

## 安装客户端

运行如下命令：
　　[root@test1 local]# rpm -ivh MySQL-client-4.0.16-0.i386.rpm
显示如下信息
　　warning: MySQL-client-4.0.16-0.i386.rpm: V3 DSA signature: NOKEY, key ID 5072e1f5
　　Preparing...　　　　########################################### [100%]
　　1:MySQL-client　 ########################################### [100%]
显示安装完毕。