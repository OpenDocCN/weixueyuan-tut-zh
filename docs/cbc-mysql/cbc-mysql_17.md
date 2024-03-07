# Mysql order by 语句的优化

在某些情况中，MySQL 可以使用一个索引来满足 ORDER BY 子句，而不需要额外的排序。where 条件和 order by 使用相同的索引，并且 order by 的顺序和索引顺序相同，并且 order by 的字段都是升序或者都是降序。

例如：下列 sql 可以使用索引。
    SELECT * FROM t1 ORDER BY key_part1,key_part2,... ;
    SELECT * FROM t1 WHERE key_part1=1 ORDER BY key_part1 DESC, key_part2 DESC;
    SELECT * FROM t1 ORDER BY key_part1 DESC, key_part2 DESC;

但是以下情况不使用索引。

1) SELECT * FROM t1 ORDER BY key_part1 DESC, key_part2 ASC；
--order by 的字段混合 ASC 和 DESC

2) SELECT * FROM t1 WHERE key2=constant ORDER BY key1；
--用于查询行的关键字与 ORDER BY 中所使用的不相同

3) SELECT * FROM t1 ORDER BY key1, key2；
--对不同的关键字使用 ORDER BY