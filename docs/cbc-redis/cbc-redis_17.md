# HBase 删除表（disable 和 drop 命令）

> 原文：[`c.biancheng.net/view/6532.html`](http://c.biancheng.net/view/6532.html)

HBase 使用 drop 命令删除表，但是在删除表之前需要先使用 disable 命令禁用表。

例如有一个 Student 表，删除该表的完整流程如下：

disable 'Student'
drop 'Student'

使用 disable 禁用表以后，可以使用 is_disable 查看表是否禁用成功。

另外，如果只是想清空表中的所有数据，使用 truncate 命令即可，此命令相当于完成禁用表、删除表，并按原结构重新建立表操作：

truncate 'Student'