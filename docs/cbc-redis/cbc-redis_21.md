# HBase scan 命令：查询全表数据

> 原文：[`c.biancheng.net/view/6537.html`](http://c.biancheng.net/view/6537.html)

HBase scan 命令用来查询全表数据，使用时只需指定表名即可。

示例：Student 数据表

| 行键 | 列族 StuInfo | 列族 Grades | 时间戳 |
| Name | Age | Sex | Class | BigData | Computer | Math |
| 0001 | Tom Green | 18 | Male |   | 80 | 90 | 85 | T2 |
| 0002 | Amy | 19 |   | 01 | 95 |   | 89 | T1 |
| 0003 | Allen | 19 | Male | 02 | 90 |   | 88 | T1 |

例如对于上面的 Student 表，使用下面的写法即可查询数据：

scan 'Student'

同样地，还可以指定列族和列的名称，或指定输出行数，甚至指定输出行键范围，如下图所示。

![](img/6d5394fb1723d080495e732a7118f339.png)scan 指定条件输出时，需要使用大括号将参数包含起来。

注意指定列族和列名称使用 COLUMN 限定符；指定输出行键范围使用 STARTROW 和 ENDROW 限定符，此时输出行不包括 ENDROW 行。例如，上图中 ENDROW=>0003，只会输出行键为 0002 的记录，不会输出 0003 记录。

上述限定条件也可以联合使用，中间用逗号隔开即可。

在 HBase 中，具有相同行键的单元格，无论其属于哪个列族，都可以将整体看作一个逻辑行， 使用 count 命令可以计算表的逻辑行数。

在关系型数据库中，有多少条记录就有多少行，表中的行数很容易统计。而在 HBase 里，计算逻辑行需要扫描全表的内容，重复的行键是不纳入计数的，且标记为 tombstone 的删除数据也不纳入计数。

执行 count 命令其实是一个开销较大的进程，特别是应用在大数据场景时，可能需要持续很长时间，因此，用户一般会结合 Hadoop 的 MapReduce 架构来进行分布式的扫描计数。