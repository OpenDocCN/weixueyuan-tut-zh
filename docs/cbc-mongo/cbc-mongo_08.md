# MongoDB 创建集合

> 原文：[`c.biancheng.net/view/6566.html`](http://c.biancheng.net/view/6566.html)

MongoDB 将文档存储在集合中。集合类似于关系数据库中的表。如果集合不存在，则 MongoDB 会在第一次存储该集合数据时创建该集合。

## MongoDB 集合的命名规则

MongoDB 的集合就相当于 MySQL 的一个表 table，MySQL 列出的所有表都可以使用 show tables，MongoDB 可以使用 show collections 展示所有集合。

集合是一组文档，是无模式的，集合名称要求符合 UTF-8 标准的字符串，同时要遵循下表所示的注意事项。

MongoDB 集合命名的注意事项

| 序号 | 注意事项 |
| 1 | 集合名不能是空串 |
| 2 | 不能含有空字符 \0 |
| 3 | 不能以“system.”开头，这是系统集合保留的前缀 |
| 4 | 集合名不能含保留字符“$” |

对于分别部署在 Windows、Linux、UNIX 系统上的 MongoDB，集合的命名方式与数据库命名方式一致。

## 创建集合

MongoDB 集合的创建有显式和隐式两种方法。

显式创建集合可通过使用`db.createCollection(name, options)`方法来实现，参数 name 指要创建的集合名称，options 是可选项，指定内存大小和索引等，下表描述 了 options 可使用的选项。

options 可以使用的选项

| 参数 | 类型 | 描述 |
| capped | Boolean | （可选）如果为 true，则启用封闭的集合。上限集合是固定大小的集合，它在达到其最大时自动覆盖其最旧的条目。如果指定 true，则还需要指定 size 参数 |
| size | 数字 | （可选）指定上限集合的最大大小（以字节为单位）。如果 capped 为 true，那么还需要指定次字段的值 |
| max | 数字 | （可选）指定上限集合中允许的最大文档数 |

注意：在插入文档时，MongoDB 首先检查上限集合 capped 字段的大小，然后检查 max 字段。

显式创建集合的一个例子：

db.createCollection("mySet", {capped:true,size:6142800, max :10000 })

在 MongoDB 中，当插入文档时，如果集合不存在，则 MongoDB 会隐式地自动创建集合，方法如下：

db.myDB.insert( {"name": "tom"} )

## 其他集合操作

创建集合后可以通过 show collections 命令查看集合的详细信息，使用 renamecollection() 方法可对集合进行重新命名。

删除集合使用 drop() 方法，具体代码如下：

Show collections;
db.mySet.renameCollection( "orders2014");
db.orders2014.drop()