# Mysql SQL Mode 详解

## Mysql SQL Mode 简介

MySQL 服务器能够工作在不同的 SQL 模式下，并能针对不同的客户端以不同的方式应用这些模式。这样，应用程序就能对服务器操作进行量身定制以满足自己的需求。这类模式定义了 MySQL 应支持的 SQL 语法，以及应该在数据上执行何种确认检查。这样，就能在众多不同的环境下、与其他数据库服务器一起更容易地使用 MySQL。可以使用“--sql-mode="modes"”选项，通过启动 mysqld 来设置默认的 SQL 模式。从 MySQL 4.1 开始，也能在启动之后，使用 SET [SESSION|GLOBAL] sql_mode='modes'语句，通过设置 sql_mode 变量更改模式。

通常在 linux 下安装完 mysql 后，默认的 sql-mode 值是空，在这种情形下 mysql 执行的是一种不严格的检查，例如日期字段可以插入’0000-00-00 00:00:00’这样的值，还有如果要插入的字段长度超过列定义的长度，那么 mysql 不会终止操作，而是会自动截断后面的字符继续插入操作，如下例：
    mysql> create table t5 (c1 char(3));
    mysql> insert into t5 values('abcd');
    mysql> select * from t5;
    +------+
    | c1   |
    +------+
    | abc  |
    +------+
    1 row in set (0.00 sec)

我们发现插入的字符被自动截断了，但是如果我们本意希望如果长度超过限制就报错，那么我们可以设置 sql_mode 为 STRICT_TRANS_TABLES，如下：
    mysql> set session sql_mode='STRICT_TRANS_TABLES'
这样我们再执行同样的操作，mysql 就会告诉我们插入的值太长，操作被终止，如下：
    mysql> insert into t5 values('abcd');
    ERROR 1406 (22001): Data too long for column 'c1' at row 1

经常使用的 sql_mode 值

| Sql_mode 值 | 描述 |
| ANSI | 更改语法和行为，使其更符合标准 SQL。 |
| STRICT_TRANS_TABLES | 如果不能将给定的值插入到事务表中，则放弃该语句。对于非事务表，如果值出现在单行语句或多行语句的第 1 行，则放弃该语句。本节后面给出了更详细的描述。 |
| TRADITIONAL | Make MySQL 的行为象“传统”SQL 数据库系统。该模式的简单描述是当在列中插入不正确的值时“给出错误而不是警告”。注释：一旦发现错误立即放弃 INSERT/UPDATE。如果你使用非事务存储引擎，这种方式不是你想要的，因为出现错误前进行的数据更改不会“滚动”，结果是更新“只进行了一部分”。 |

说明：如果把 sql_mode 的值设置成后面的两个值（也就是我们说的严格模式），那么当在列中插入或更新不正确的值时，mysql 将会给出错误，并且放弃 insert/update 操作。在我们的一般应用中建议使用这两种模式，而不是使用默认的空或 ANSI 模式。但是需要注意的问题是，如果数据库运行在严格模式下，并且你的存储引擎不支持事务，那么有数据不一致的风险存在，比如一组 sql 中有两个 dml 语句，如果后面的一个出现了问题，但是前面的已经操作成功，那么 mysql 并不能回滚前面的操作。因此说设置 sql_mode 需要应用人员权衡各种得失，从而得到一个合适的选择。

Sql_mode 的值还有很多，这里不再累述，可以参考相关的手册。

## SQL Mode 与可移植性

如果 mysql 与其它异构数据库之间有数据移植的需求的话，那么下面的 sql_mode 的组合设置可以达到相应的效果：

| 数据库 | Sql_mode 值 |
| DB2 | PIPES_AS_CONCAT、ANSI_QUOTES、IGNORE_SPACE、NO_KEY_OPTIONS、NO_TABLE_OPTIONS、NO_FIELD_OPTIONS |
| MAXDB | PIPES_AS_CONCAT、ANSI_QUOTES、IGNORE_SPACE、NO_KEY_OPTIONS、NO_TABLE_OPTIONS、NO_FIELD_OPTIONS、 NO_AUTO_CREATE_USER |
| MSSQL | PIPES_AS_CONCAT、ANSI_QUOTES、IGNORE_SPACE、NO_KEY_OPTIONS、NO_TABLE_OPTIONS、 NO_FIELD_OPTIONS |
| ORACLE | PIPES_AS_CONCAT、ANSI_QUOTES、IGNORE_SPACE、NO_KEY_OPTIONS、NO_TABLE_OPTIONS、NO_FIELD_OPTIONS、NO_AUTO_CREATE_USER |
| POSTGRESQL | PIPES_AS_CONCAT、ANSI_QUOTES、IGNORE_SPACE、NO_KEY_OPTIONS、NO_TABLE_OPTIONS、NO_FIELD_OPTIONS |

## SQL Mode 与数据效验

SQL Mode 还可以实现对数据效验和转移等功能如：

1.  效验日期数据合法性.
2.  在 INSERT 或 UPDATE 过程中，如果被零除(或 MOD(X，0))，则产生错误
3.  将‘"’视为识别符引号(‘`’引号字符)
4.  禁用反斜线字符(‘\’)做为字符串内的退出字符。启用 NO_BACKSLASH_ESCAPES 模式，反斜线则成为普通字符。
5.  将||视为字符串连接操作符（＋）(同 CONCAT())，而不视为 OR。