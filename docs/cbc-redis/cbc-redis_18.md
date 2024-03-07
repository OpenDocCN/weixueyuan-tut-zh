# HBase put 命令：插入数据

> 原文：[`c.biancheng.net/view/6534.html`](http://c.biancheng.net/view/6534.html)

HBase 使用 put 命令向数据表中插入数据，put 向表中增加一个新行数据，或覆盖指定行的数据。

示例：Student 数据表

| 行键 | 列族 StuInfo | 列族 Grades | 时间戳 |
| Name | Age | Sex | Class | BigData | Computer | Math |
| 0001 | Tom Green | 18 | Male |   | 80 | 90 | 85 | T2 |
| 0002 | Amy | 19 |   | 01 | 95 |   | 89 | T1 |
| 0003 | Allen | 19 | Male | 02 | 90 |   | 88 | T1 |

例如有以上结构的数据表，向其中插入一条数据的写法为：

put 'Student', '0001', 'Stulnfo:Name', 'Tom Green', 1

在上述命令中：

*   第一个参数`Student`为表名；
*   第二个参数`0001`为行键的名称，为字符串类型；
*   第三个参数`StuInfo:Name`为列族和列的名称，中间用冒号隔开。列族名必须是已经创建的，否则 HBase 会报错；列名是临时定义的，因此列族里的列是可以随意扩展的；
*   第四个参数`Tom Green`为单元格的值。在 HBase 里，所有数据都是字符串的形式；
*   最后一个参数`1`为时间戳，如果不设置时间戳，则系统会自动插入当前时间为时间戳。

注意，put 命令只能插入一个单元格的数据，上表中的一行数据需要通过以下几条命令一起完成：

put 'Student', '0001', 'StuInfo:Name', 'Tom Green', 1
put 'Student', '0001', 'StuInfo:Age', '18'
put 'Student', '0001', 'StuInfo:Sex', 'Male'
put 'Student', '0001', 'Grades:BigData', '80'
put 'Student', '0001', 'Grades:Computer', '90'
put 'Student', '0001', 'Grades:Math', '85'

如果 put 语句中的单元格是已经存在的，即行键、列族及列名都已经存在，且不考虑时间戳的情况下，执行 put 语句，则可对数据进行更新操作。

如以下命令可将行键为 0001 的学生姓名改为 Jim Green：

put 'Student', '0001', 'Stulnfo:Name', 'Jim Green'

如果在初始创建表时，已经设定了列族 VERSIONS 参数值为 n，则 put 操作可以保存 n 个版本数据，即可查询到行键为 0001 的学生的 n 个版本的姓名数据。