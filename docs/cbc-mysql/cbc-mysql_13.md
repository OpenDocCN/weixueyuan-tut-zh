# Mysql 索引优化教程

## 索引的存储分类

myisam 表的数据文件和索引文件是自动分开的；innodb 的数据和索引是存储在同一个表空间里面，但可以有多个文件组成。 创建索引语法如下：
    CREATE [UNIQUE|FULLTEXT|SPATIAL] INDEX index_name
    [USING index_type]
    ON tbl_name (index_col_name,...)
    index_col_name:
    col_name [(length)] [ASC | DESC]

索引的存储类型目前只有两种（btree 和 hash），具体和表的模式相关：
    myisam     btree
    innodb      btree
    memory/heap hash，btree

mysql 目前不支持函数索引，只能对列的前一部分（length）进行索引，例：
    create index ind_test on table1(name(5))，
对于 char 和 varchar 列，使用前缀索引将大大节省空间。

## MySQL 如何使用索引

索引用于快速找出在某个列中有一特定值的行。对相关列使用索引是提高 SELECT 操作性能的最佳途径。
查询要使用索引最主要的条件是查询条件中需要使用索引关键字，如果是多列索引，那么只有查询条件使用了多列关键字最左边的前缀时，才可以使用索引，否则将不能使用索引。

**下列情况下，Mysql 不会使用已有的索引：**
1.如果 mysql 估计使用索引比全表扫描更慢，则不使用索引。例如：如果 key_part1 均匀分布在 1 和 100 之间，下列查询中使用索引就不是很好：
    SELECT * FROM table_name where key_part1 > 1 and key_part1 < 90
2.如果使用 heap 表并且 where 条件中不用＝索引列，其他> 、<、 >=、 <=均不使用索引；
3.如果不是索引列的第一部分；
4.如果 like 是以％开始；
5.对 where 后边条件为字符串的一定要加引号，字符串如果为数字 mysql 会自动转为字符串，但是不使用索引。

## 查看索引使用情况

如果索引正在工作，Handler_read_key 的值将很高，这个值代表了一个行被索引值读的次数，很低的值表明增加索引得到的性能改善不高，因为索引并不经常使用。

Handler_read_rnd_next 的值高则意味着查询运行低效，并且应该建立索引补救。这个值的含义是在数据文件中读下一行的请求数。如果你正进行大量的表扫描，该值较高。通常说明表索引不正确或写入的查询没有利用索引。语法：
    mysql> show status like 'Handler_read%';