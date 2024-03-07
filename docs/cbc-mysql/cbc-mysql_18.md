# Mysql join 语句的优化

Mysql4.1 开始支持 SQL 的子查询。这个技术可以使用 SELECT 语句来创建一个单列的查询结果，然后把这个结果作为过滤条件用在另一个查询中。使用子查询可以一次性的完成很多逻辑上需要多个步骤才能完成的 SQL 操作，同时也可以避免事务或者表锁死，并且写起来也很容易。但是，有些情况下，子查询可以被更有效率的连接 JOIN 替代。

假设我们要将所有没有订单记录的用户取出来，可以用下面这个查询完成：
    SELECT * FROM customerinfo WHERE CustomerID NOT in (SELECT CustomerID FROM salesinfo )

如果使用连接 JOIN 来完成这个查询工作，速度将会快很多。尤其是当 salesinfo 表中对 CustomerID 建有索引的话，性能将会更好，查询如下：
    SELECT * FROM customerinfo
    LEFT JOIN salesinfoON customerinfo.CustomerID=salesinfo.CustomerID
    WHERE salesinfo.CustomerID IS NULL 

连接 JOIN 之所以更有效率一些，是因为 MySQL 不需要在内存中创建临时表来完成这个逻辑上的需要两个步骤的查询工作。