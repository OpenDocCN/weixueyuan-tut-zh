# Mysql group by 语句的优化

默认情况下，MySQL 排序所有 GROUP BY col1, col2, ....，查询的方法如同在查询中指定 ORDER BY  col1, col2, ...。如果显式包括一个包含相同的列的 ORDER BY 子句，MySQL 可以毫不减速地对它进行优化，尽管仍然进行排序。

如果查询包括 GROUP BY 但你想要避免排序结果的消耗，你可以指定 ORDER BY NULL 禁止排序。例如：
    INSERT INTO foo
    SELECT a, COUNT(*) FROM bar GROUP BY a ORDER BY NULL;