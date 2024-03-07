# HBase 删除数据（delete 命令）

> 原文：[`c.biancheng.net/view/6535.html`](http://c.biancheng.net/view/6535.html)

HBase delete 命令可以从表中删除一个单元格或一个行集，语法与 put 类似，必须指明表名和列族名称，而列名和时间戳是可选的。

示例：Student 数据表

| 行键 | 列族 StuInfo | 列族 Grades | 时间戳 |
| Name | Age | Sex | Class | BigData | Computer | Math |
| 0001 | Tom Green | 18 | Male |   | 80 | 90 | 85 | T2 |
| 0002 | Amy | 19 |   | 01 | 95 |   | 89 | T1 |
| 0003 | Allen | 19 | Male | 02 | 90 |   | 88 | T1 |

例如，执行以下命令，将删除 Student 表中行键为 0002 的 Grades 列族的所有数据：

delete 'Student', '0002', 'Grades'

需要注意的是，delete 操作并不会马上删除数据，只会将对应的数据打上删除标记（tombstone），只有在合并数据时，数据才会被删除。

另外，delete 命令的最小粒度是单元格（Cell）。例如，执行以下命令将删除 Student 表中行键为 0001，Grades 列族成员为 Math，时间戳小于等于 2 的数据：

delete 'Student', '0001', 'Grades:Math', 2

delete 命令不能跨列族操作，如需删除表中所有列族在某一行上的数据，即删除上表中一个逻辑行，则需要使用 deleteall 命令，如下所示，不需要指定列族和列的名称：

deleteall 'Student', '0001'