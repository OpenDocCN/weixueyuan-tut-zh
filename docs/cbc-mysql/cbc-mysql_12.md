# Mysql 下优化 SQL 的一般步骤

## 通过 show status 和应用特点了解各种 SQL 的执行频率

通过 SHOW STATUS 可以提供服务器状态信息，也可以使用 mysqladmin extended-status 命令获得。SHOW STATUS 可以根据需要显示 session 级别的统计结果和 global 级别的统计结果。

**以下几个参数对 Myisam 和 Innodb 存储引擎都计数：**

1.  Com_select  执行 select 操作的次数，一次查询只累加 1；
2.  Com_insert 执行 insert 操作的次数，对于批量插入的 insert 操作，只累加一次；
3.  Com_update 执行 update 操作的次数；
4.  Com_delete　执行 delete 操作的次数。

**以下几个参数是针对 Innodb 存储引擎计数的，累加的算法也略有不同：**

1.  Innodb_rows_read　select 查询返回的行数；
2.  Innodb_rows_inserted 执行 Insert 操作插入的行数；
3.  Innodb_rows_updated 执行 update 操作更新的行数；
4.  Innodb_rows_deleted　执行 delete 操作删除的行数。

通过以上几个参数，可以很容易的了解当前数据库的应用是以插入更新为主还是以查询操作为主，以及各种类型的 SQL 大致的执行比例是多少。对于更新操作的计数，是对执行次数的计数，不论提交还是回滚都会累加。

对于事务型的应用，通过 Com_commit 和 Com_rollback 可以了解事务提交和回滚的情况，对于回滚操作非常频繁的数据库，可能意味着应用编写存在问题。

此外，以下几个参数便于我们了解数据库的基本情况：

*   Connections 试图连接 Mysql 服务器的次数
*   Uptime　   服务器工作时间
*   Slow_queries 慢查询的次数

## 定位执行效率较低的 SQL 语句

可以通过以下两种方式定位执行效率较低的 SQL 语句：

*   可以通过慢查询日志定位那些执行效率较低的 sql 语句，用--log-slow-queries[=file_name]选项启动时，mysqld 写一个包含所有执行时间超过 long_query_time 秒的 SQL 语句的日志文件。可以链接到管理维护中的相关章节。
*   慢查询日志在查询结束以后才纪录，所以在应用反映执行效率出现问题的时候查询慢查询日志并不能定位问题，可以使用 show processlist 命令查看当前 MySQL 在进行的线程，包括线程的状态，是否锁表等等，可以实时的查看 SQL 执行情况，同时对一些锁表操作进行优化。

## 通过 EXPLAIN 分析低效 SQL 的执行计划

通过以上步骤查询到效率低的 SQL 后，我们可以通过 explain 或者 desc 获取 MySQL 如何执行 SELECT 语句的信息，包括 select 语句执行过程表如何连接和连接的次序。

explain 可以知道什么时候必须为表加入索引以得到一个使用索引来寻找记录的更快的 SELECT。

mysql> explain select sum(moneys) from sales a,companys b where a.company_id = b.id and a.year = 2006;
+----------------+----------+-----------+----------------+----------------+----------+-----------+----------------+
| select_type   | table | type  | possible_keys| key            | key_len   | rows  | Extra     |
+----------------+----------+-----------+----------------+----------------+----------+-----------+----------------+
| SIMPLE    | b     | index | PRIMARY   | PRIMARY   | 4     |    1  | Using index   |
| SIMPLE    | a     | ALL   | NULL      | NULL      | NULL  |   12  | Using where   |
+----------------+----------+-----------+----------------+----------------+----------+-----------+----------------+
2 rows in set (0.02 sec)

说明：

*   select_type：select 类型
*   table：输出结果集的表
*   type：表示表的连接类型
    *   ①当表中仅有一行是 type 的值为 system 是最佳的连接类型；
    *   ②当 select 操作中使用索引进行表连接时 type 的值为 ref；
    *   ③当 select 的表连接没有使用索引时，经常会看到 type 的值为 ALL，表示对该表进行了全表扫描，这时需要考虑通过创建索引来提高表连接的效率。
*   possible_keys：表示查询时,可以使用的索引列.
*   key：表示使用的索引
*   key_len：索引长度
*   rows：扫描范围
*   Extra：执行情况的说明和描述

## 确定问题，并采取相应的优化措施

经过以上步骤，基本可以确认问题出现的原因，可以根据情况采取相应的措施，进行优化提高执行的效率。

例如上面的例子，我们确认是对 a 表的全表扫描导致效率的不理想，我们对 a 表的 year 字段创建了索引，查询需要扫描的行数明显较少。

mysql> explain select sum(moneys) from sales a,companys b where a.company_id = b.id and a.year = 2006;
+----------------+----------+-----------+----------------+----------------+----------+-----------+----------------+
| select_type   | table | type  | possible_keys| key            | key_len   | rows  | Extra     |
+----------------+----------+-----------+----------------+----------------+----------+-----------+----------------+
| SIMPLE    | b     | index | PRIMARY   | PRIMARY   | 4     |    1  | Using index   |
| SIMPLE    | a     | ref       | year      | year      | 4     |    3  | Using where   |
+----------------+----------+-----------+----------------+----------------+----------+-----------+----------------+
2 rows in set (0.02 sec)