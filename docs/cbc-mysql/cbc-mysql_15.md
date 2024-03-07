# Mysql insert 语句的优化

1) 如果你同时从同一客户插入很多行，使用多个值表的 INSERT 语句。这比使用分开 INSERT 语句快(在一些情况中几倍)。
    Insert into test values(1,2),(1,3),(1,4)…

2) 如果你从不同客户插入很多行，能通过使用 INSERT DELAYED 语句得到更高的速度。Delayed 的含义是让 insert 语句马上执行，其实数据都被放在内存的队列中，并没有真正写入磁盘；这比每条语句分别插入要快的多；LOW_PRIORITY 刚好相反，在所有其他用户对表的读写完后才进行插入。

3) 将索引文件和数据文件分在不同的磁盘上存放（利用建表中的选项）。

4) 如果进行批量插入，可以增加 bulk_insert_buffer_size 变量值的方法来提高速度，但是，这只能对 myisam 表使用。

5) 当从一个文本文件装载一个表时，使用 LOAD DATA INFILE。这通常比使用很多 INSERT 语句快 20 倍。

6) 根据应用情况使用 replace 语句代替 insert。

7) 根据应用情况使用 ignore 关键字忽略重复记录。