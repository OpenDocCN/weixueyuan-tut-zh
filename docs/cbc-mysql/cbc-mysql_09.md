# Mysql 锁机制和事务控制

## 如何加锁

锁定表的语法：
    LOCK TABLES
    tbl_name [AS alias] {READ [LOCAL] | [LOW_PRIORITY] WRITE}
    [, tbl_name [AS alias] {READ [LOCAL] | [LOW_PRIORITY] WRITE}] ...
解锁语法：
    UNLOCK TABLES

innodb 的存储引擎提供行级锁，支持共享锁和排他锁两种锁定模式，以及四种不同的隔离级别。

## 死锁

InnoDB 自动检测事务的死锁，并回滚一个或几个事务来防止死锁。InnoDB 不能在 MySQL LOCK TABLES 设定表锁定的地方或者涉及 InnoDB 之外的存储引擎设置锁定的地方检测死锁。你必须通过设定 innodb_lock_wait_timeout 系统变量的值来解决这些情况。如果要依靠锁等待超时来解决死锁问题，对于更新事务密集的应用，将有可能导致大量事务的锁等待，导致系统异常，所以不推荐在一个事务中混合更新不同存储类型的表，也不推荐相同类型的表采用不同的锁定方式加锁。

## 事务控制

MySQL 通过 SET AUTOCOMMIT, START TRANSACTION, COMMIT 和 ROLLBACK 等语句支持本地事务。语法：
    START TRANSACTION | BEGIN [WORK]
    COMMIT [WORK] [AND [NO] CHAIN] [[NO] RELEASE]
    ROLLBACK [WORK] [AND [NO] CHAIN] [[NO] RELEASE]
    SET AUTOCOMMIT = {0 | 1}
默认情况下，mysql 是 autocommit 的，如果需要通过明确的 commit 和 rollback 来提交和回滚事务，那么需要通过明确的事务控制命令来开始事务，这是和 oracle 的事务管理明显不同的地方，如果应用是从 oracle 数据库迁移到 mysql 数据库，则需要确保应用中是否对事务进行了明确的管理。

1.  START TRANSACTION 或 BEGIN 语句可以开始一项新的事务。
2.  COMMIT 和 ROLLBACK 用来提交或者回滚事务。
3.  CHAIN 和 RELEASE 子句分别用来定义在事务提交或者回滚之后的操作，chain 会立即启动一个新事物，并且和刚才的事务具有相同的隔离级别，release 则会断开和客户端的连接。
4.  SET AUTOCOMMIT 可以修改当前连接的提交方式，如果设置了 SET AUTOCOMMIT=0，则设置之后的所有事务都需要通过明确的命令进行提交或者回滚。

如果我们只是对某些语句需要进行事务控制，则使用 START TRANSACTION 开始一个事务比较方便，这样事务结束之后可以自动回到自动提交的方式，如果我们希望我们所有的事务都不是自动提交的，那么通过修改 AUTOCOMMIT 来控制事务比较方便，这样不用在每个事务开始的时候再执行 START TRANSACTION。

| time | session_1 | session_2 |
| ---------------------------------------------------------> | mysql> select * from tt3; Empty set (0.00 sec) | mysql> select * from tt3; Empty set (0.00 sec) |
| mysql> start transaction; Query OK, 0 rows affected (0.00 sec)
mysql> insert into tt3 values('1',1);
Query OK, 1 row affected (0.03 sec) |   |
|   | mysql> select * from tt3; Empty set (0.00 sec) |
| mysql> commit; Query OK, 0 rows affected (0.05 sec) |   |
|   | mysql> select * from tt3; +------+------+
&#124; id   &#124; name &#124;
+------+------+
&#124; 1    &#124; 1.00 &#124;
+------+------+
1 row in set (0.00 sec) |
| mysql> insert into tt3 values('2',2); Query OK, 1 row affected (0.04 sec)
这个事务是按照自动提交执行的 |   |
|   | mysql> select * from tt3; +------+------+
&#124; id   &#124; name &#124;
+------+------+
&#124; 1    &#124; 1.00 &#124;
&#124; 2    &#124; 2.00 &#124;
+------+------+
2 rows in set (0.00 sec) |
| mysql> start transaction; Query OK, 0 rows affected (0.00 sec)

mysql> insert into tt3 values('3',3);
Query OK, 1 row affected (0.00 sec)

mysql> commit and chain;
Query OK, 0 rows affected (0.05 sec)
自动开始一个新的事务
mysql> insert into tt3 values('4',4);
Query OK, 1 row affected (0.00 sec) |   |
|   | mysql> select * from tt3; +------+------+
&#124; id   &#124; name &#124;
+------+------+
&#124; 1    &#124; 1.00 &#124;
&#124; 2    &#124; 2.00 &#124;
&#124; 3    &#124; 3.00 &#124;
+------+------+
3 rows in set (0.00 sec) |
| mysql> commit; Query OK, 0 rows affected (0.06 sec) |   |
|   | mysql> select * from tt3; +------+------+
&#124; id   &#124; name &#124;
+------+------+
&#124; 1    &#124; 1.00 &#124;
&#124; 2    &#124; 2.00 &#124;
&#124; 3    &#124; 3.00 &#124;
&#124; 4    &#124; 4.00 &#124;
+------+------+
4 rows in set (0.00 sec) |

开始一个事务，会造成一个隐含的 unlock tables 被执行：

| time | session_1 | session_2 |
| ---------------------------------------------------------> | mysql> select * from tt3; Empty set (16.65 sec) | mysql> select * from tt3; Empty set (16.65 sec) |
| mysql> lock table tt3 write; Query OK, 0 rows affected (0.00 sec) |   |
|   | mysql> select * from tt3; 等待 |
| mysql> insert into tt3 values('1',1); Query OK, 1 row affected (0.07 sec) | 等待 |
| mysql> rollback; Query OK, 0 rows affected (0.00 sec) | 等待 |
| mysql> start transaction; Query OK, 0 rows affected (0.00 sec) | 等待 |
|   | mysql> select * from tt3; +------+------+
&#124; id   &#124; name &#124;
+------+------+
&#124; 1    &#124; 1.00 &#124;
+------+------+
1 row in set (37.71 sec)
开始一个事务时，表锁被释放。
对 lock 方式加的表锁，不能通过 rollback 进行回滚。 |

因此，在同一个事务中，最好不使用不同存储引擎的表，否则 rollback 时需要对非事务类型的表进行特别的处理，因为 commit、rollback 只能对事务类型的表进行提交和回滚。
通常情况下，只对提交的事务纪录到二进制的日志中，但是如果一个事务中包含非事务类型的表，那么回滚操作也会被记录到二进制日志中，以确保非事务类型表的更新可以被复制到从的数据库中。

和 oracle 的事务管理相同，所有的 DDL 语句是不能回滚的，并且部分的 DDL 语句会造成隐式的提交。

在事务中可以通过定义 savepoint，指定回滚事务的一个部分，但是不能指定提交事务的一个部分。对于复杂的应用，可以定义多个不同的 savepoint，满足不同的条件时，回滚不同的 savepoint。需要注意的是，如果定义了相同名字的 savepoint，则后面定义的 savepoint 会覆盖之前的定义。对于不再需要使用的 savepoint，可以通过 release savepoint 命令删除 savepoint，删除后的 savepoint，不能再执行 rollback to savepoint 命令。

下面我们例子就是模拟回滚事务的一个部分，通过定义 savepoint 来指定需要回滚的事务的位置。

| time | session_1 | session_2 |
| ---------------------------------------------------------> | mysql> select * from tt3; +------+------+
&#124; id   &#124; name &#124;
+------+------+
&#124; 2    &#124; 2.00 &#124;
&#124; 3    &#124; 3.00 &#124;
&#124; 4    &#124; 4.00 &#124;
+------+------+
3 rows in set (0.00 sec) | mysql> select * from tt3; +------+------+
&#124; id   &#124; name &#124;
+------+------+
&#124; 2    &#124; 2.00 &#124;
&#124; 3    &#124; 3.00 &#124;
&#124; 4    &#124; 4.00 &#124;
+------+------+
3 rows in set (0.00 sec) |
| mysql> start transaction; Query OK, 0 rows affected (0.00 sec)

mysql> delete from tt3 where id = '2';
Query OK, 1 row affected (0.00 sec) |   |
| mysql> select * from tt3; +------+------+
&#124; id   &#124; name &#124;
+------+------+
&#124; 3    &#124; 3.00 &#124;
&#124; 4    &#124; 4.00 &#124;
+------+------+
3 rows in set (0.00 sec) | mysql> select * from tt3; +------+------+
&#124; id   &#124; name &#124;
+------+------+
&#124; 2    &#124; 2.00 &#124;
&#124; 3    &#124; 3.00 &#124;
&#124; 4    &#124; 4.00 &#124;
+------+------+
3 rows in set (0.00 sec) |
| mysql> savepoint test; Query OK, 0 rows affected (0.00 sec)

mysql> delete from tt3 where id = '3';
Query OK, 1 row affected (0.00 sec) |   |
| mysql> select * from tt3; +------+------+
&#124; id   &#124; name &#124;
+------+------+
&#124; 4    &#124; 4.00 &#124;
+------+------+
3 rows in set (0.00 sec) | mysql> select * from tt3; +------+------+
&#124; id   &#124; name &#124;
+------+------+
&#124; 2    &#124; 2.00 &#124;
&#124; 3    &#124; 3.00 &#124;
&#124; 4    &#124; 4.00 &#124;
+------+------+
3 rows in set (0.00 sec) |
| mysql> rollback to savepoint test; Query OK, 0 rows affected (0.00 sec) |   |
| mysql> select * from tt3; +------+------+
&#124; id   &#124; name &#124;
+------+------+
&#124; 3    &#124; 3.00 &#124;
&#124; 4    &#124; 4.00 &#124;
+------+------+
2 rows in set (0.00 sec) | mysql> select * from tt3; +------+------+
&#124; id   &#124; name &#124;
+------+------+
&#124; 2    &#124; 2.00 &#124;
&#124; 3    &#124; 3.00 &#124;
&#124; 4    &#124; 4.00 &#124;
+------+------+
3 rows in set (0.00 sec) |
| mysql> commit; Query OK, 0 rows affected (0.05 sec) |   |
| mysql> select * from tt3; +------+------+
&#124; id   &#124; name &#124;
+------+------+
&#124; 3    &#124; 3.00 &#124;
&#124; 4    &#124; 4.00 &#124;
+------+------+
2 rows in set (0.00 sec) | mysql> select * from tt3; +------+------+
&#124; id   &#124; name &#124;
+------+------+
&#124; 3    &#124; 3.00 &#124;
&#124; 4    &#124; 4.00 &#124;
+------+------+
2 rows in set (0.00 sec) |