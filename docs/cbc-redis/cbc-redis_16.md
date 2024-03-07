# HBase 修改表（alter 命令）

> 原文：[`c.biancheng.net/view/6531.html`](http://c.biancheng.net/view/6531.html)

HBase 表的结构和表的管理可以通过 alter 命令来完成，使用这个命令可以完成更改列族参数信息、增加列族、删除列族以及更改表的相关设置等操作。

示例：Student 数据表

| 行键 | 列族 StuInfo | 列族 Grades | 时间戳 |
| Name | Age | Sex | Class | BigData | Computer | Math |
| 0001 | Tom Green | 18 | Male |   | 80 | 90 | 85 | T2 |
| 0002 | Amy | 19 |   | 01 | 95 |   | 89 | T1 |
| 0003 | Allen | 19 | Male | 02 | 90 |   | 88 | T1 |

## 修改列族

首先修改列族的参数信息，如修改列族的版本。例如上面的 Student 表，假设它的列族 Grades 的 VERSIONS 为 1，但是实际可能需要保存最近的 3 个版本，可使用以下命令完成：

alter 'Student', {NAME => 'Grades', VERSIONS => 3}

修改多个列族的参数，形式与 create 命令类似。

这里要注意，修改已存有数据的列族属性时，HBase 需要对列族里所有的数据进行修改，如果数据量很大，则修改可能要占很长时间。

## 增加列族

如果需要在 Student 表中新增一个列族 hobby，使用以下命令：

alter 'Student', 'hobby'

## 删除列族

如果要移除或者删除已有的列族，以下两条命令均可完成：

alter 'Student', { NAME => 'hobby', METHOD => 'delete' }

alter 'Student', 'delete' => 'hobby'

另外，HBase 表至少要包含一个列族，因此当表中只有一个列族时，无法将其删除。