# 教你如何调整 Mysql 中 insert、update、delete 的顺序来以提高效率

MySQL 还允许改变语句调度的优先级，它可以使来自多个客户端的查询更好地协作，这样单个客户端就不会由于锁定而等待很长时间。改变优先级还可以确保特定类型的查询被处理得更快。

我们首先应该确定应用的类型，判断应用是以查询为主还是以更新为主的，是确保查询效率还是确保更新的效率，决定是查询优先还是更新优先。

下面我们提到的改变调度策略的方法主要是针对 Myisam 存储引擎的，对于 Innodb 存储引擎，语句的执行是由获得行锁的顺序决定的。

**MySQL 的默认的调度策略可用总结如下：**

*   写入操作优先于读取操作。
*   对某张数据表的写入操作某一时刻只能发生一次，写入请求按照它们到达的次序来处理。
*   对某张数据表的多个读取操作可以同时地进行。

**MySQL 提供了几个语句调节符，允许你修改它的调度策略：**

*   LOW_PRIORITY 关键字应用于 DELETE、INSERT、LOAD DATA、REPLACE 和 UPDATE。
*   HIGH_PRIORITY 关键字应用于 SELECT 和 INSERT 语句。
*   DELAYED 关键字应用于 INSERT 和 REPLACE 语句。

如果写入操作是一个 LOW_PRIORITY（低优先级）请求，那么系统就不会认为它的优先级高于读取操作。在这种情况下，如果写入者在等待的时候，第二个读取者到达了，那么就允许第二个读取者插到写入者之前。只有在没有其它的读取者的时候，才允许写入者开始操作。这种调度修改可能存在 LOW_PRIORITY 写入操作永远被阻塞的情况。

SELECT 查询的 HIGH_PRIORITY（高优先级）关键字也类似。它允许 SELECT 插入正在等待的写入操作之前，即使在正常情况下写入操作的优先级更高。另外一种影响是，高优先级的 SELECT 在正常的 SELECT 语句之前执行，因为这些语句会被写入操作阻塞。

如果你希望所有支持 LOW_PRIORITY 选项的语句都默认地按照低优先级来处理，那么请使用--low-priority-updates 选项来启动服务器。通过使用 INSERT HIGH_PRIORITY 来把 INSERT 语句提高到正常的写入优先级，可以消除该选项对单个 INSERT 语句的影响。