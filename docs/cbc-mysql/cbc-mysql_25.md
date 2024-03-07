# Mysq 日志管理(错误日志、查询日志、慢查询日志)

## 错误日志

记录内容：包含了当 mysqld 启动和停止时，以及服务器在运行过程中发生任何严重错误时的相关信息

文件位置和格式：可以用--log-error[=file_name]选项来指定 mysqld 保存错误日志文件的位置。如果没有给定 file_name 值，mysqld 使用错误日志名 host_name.err 并在数据目录中写入日志文件

## BINLOG

记录内容：二进制日志包含了所有更新了数据或者已经潜在更新了数据（例如，没有匹配任何行的一个 DELETE）的所有语句。语句以“事件”的形式保存，它描述数据更改情况。

文件位置和格式：当用--log-bin[=file_name]选项启动时，mysqld 写入包含所有更新数据的 SQL 命令的日志文件。如果未给出 file_name 值， 默认名为-bin 后面所跟的主机名。如果给出了文件名，但没有包含路径，则文件被写入数据目录。

查看 binlog 内容：shell> mysqlbinlog log-file

删除日志：
    RESET MASTER;//删除所有 binlog 日志，新日志编号从头开始
    PURGE MASTER LOGS TO 'mysql-bin.010';//删除 mysql-bin.010 之前所有日志
    PURGE MASTER LOGS BEFORE '2003-04-02 22:46:26'
    ;// 删除 2003-04-02 22:46:26 之前产生的所有日志

相关选项：
1) --binlog-do-db=db_name
告诉主服务器，如果当前的数据库(即 USE 选定的数据库)是 db_name，应将更新记录到二进制日志中。其它所有没有明显指定的数据库  被忽略

2) --binlog-ignore-db=db_name
告诉主服务器，如果当前的数据库(即 USE 选定的数据库)是 db_name，不应将更新保存到二进制日志中要想记录或忽视多个数据库，使用多个选项，为每个数据库指定相应的选项。

3) -innodb-safe-binlog
使用此选项和 sync_binlog＝N（每写 N 次日志同步磁盘）全局变量将使得事务能够记录的更加安全

4) 具有 SUPER 权限的客户端可以通过 SET SQL_LOG_BIN=0 语句禁止将自己的语句记入二进制记录

## 查询日志

记录内容：记录了客户端的所有语句，而二进制日志不包含只查询数据的语句

文件位置和格式：用--log[=file_name]或-l [file_name]选项启动它。如果没有给定 file_name 的值， 默认名是 host_name.log。

## 慢查询日志：

记录内容：记录包含所有执行时间超过 long_query_time 秒的 SQL 语句的日志文件。获得初使表锁定的时间不算作执行时间。

文件位置和格式：用--log-slow-queries[=file_name]选项启动它。如果没有给出 file_name 值， 默认为主机名，后缀为-slow.log。如果给出了文件名，但不是绝对路径名，文件则写入数据目录。

快速查看：使用 mysqldumpslow 命令获得日志中显示的查询摘要来处理慢查询日志，例如：
    [zzx@bj37 data]$ mysqldumpslow bj37-slow.log

其他选项：在 MySQL 5.1 中,通过--log-slow-admin-statements 服务器选项，你可以请求将慢管理语句，例如 OPTIMIZE TABLE、ANALYZE TABLE 和 ALTER TABLE 写入慢查询日志