# HBase get 命令：从表中获取数据

> 原文：[`c.biancheng.net/view/6536.html`](http://c.biancheng.net/view/6536.html)

HBase get 命令可以从数据表中获取某一行记录，类似于关系型数据库中的 select 操作。get 命令必须设置表名和行键名，同时可以选择指明列族名称、时间戳范围、数据版本等参数。

示例：Student 数据表

| 行键 | 列族 StuInfo | 列族 Grades | 时间戳 |
| Name | Age | Sex | Class | BigData | Computer | Math |
| 0001 | Tom Green | 18 | Male |   | 80 | 90 | 85 | T2 |
| 0002 | Amy | 19 |   | 01 | 95 |   | 89 | T1 |
| 0003 | Allen | 19 | Male | 02 | 90 |   | 88 | T1 |

例如，对于上面的数据表，执行以下命令可以获取 Student 表中行键为 0001 的所有列族数据：

get 'student', '0001'

下图展示了在 get 语句中使用限定词 VERSIONS 后获取的数据内容。

![](img/82fff652e96dc42e5e7fb51b9d2b545c.png)上图中首先 put 三条数据，因为初始创建 Student 表和 Stulnfo 列族时，已经设定 VERSIONS 为 3，即使用 get 获取数据时最多得到 3 个版本的数据。

上图中的 get 命令使用了限定词 VERSIONS=>2，因此 get 得到的数据只显示了 Stulnfo:Name 最小的两个版本。