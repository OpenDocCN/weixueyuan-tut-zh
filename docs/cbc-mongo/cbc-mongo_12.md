# MongoDB 删除数据：remove()和 delete()方法

> 原文：[`c.biancheng.net/view/6554.html`](http://c.biancheng.net/view/6554.html)

MongoDB 使用 remove() 和 delete() 方法来删除集合中的文档。

## remove() 方法

如果不再需要 MongoDB 中存储的文档，可以通过删除命令将其永久删除。删除 MongoDB 集合中的数据可以使用 remove() 函数。

remove() 函数可以接受一个查询文档作为可选参数来有选择性地删除符合条件的文档。删除文档是永久性的，不能撤销，也不能恢复。因此，在执行 remove() 函数前最好先用 find() 命令来查看是否正确。

remove() 方法的基本语法格式如下所示：

db.collection.remove(
    <query>,
    {
        justOne: <boolean>, writeConcern: <document>
    }
)

参数说明：

*   query：必选项，是设置删除的文档的条件。
*   justOne：布尔型的可选项，默认为 false，删除符合条件的所有文档，如果设为 true，则只删除一个文档。
*   writeConcem：可选项，设置抛出异常的级别。

下面举例说明删除集合中的文档，先进行两次插入操作，代码如下：

```

>db.test.insert(
    {
        title : 'MongoDB',
        description : 'MongoDB 是一个 NoSQL 数据库',
        by : 'C 语言中文网',
        tags : ['mongodb', 'database', 'NoSQL'],
        likes : 100
    }
)
```

使用 find() 函数查询的代码如下：

```

> db.test.find()
{ "_id" : Objectld ("5ba9d8b：L24857a5fefclfde6"), "titlen : "MongoDB", "description" : "MongoDB 是一个 NoSQL 数据库", "by" : "C 语言中文网", "tags" : [ "mongodb", "database", "NoSQL" ], "Tikes" : 100 }
{ "_id" : ObjectId("5ba9d90924857a5fefclfde7"), "title" : "MongoDB ", "description" : "MongoDB 是一个 NoSQL 数据库", "by" : "C 语言中文网", "tags" : [ "mongodb", "database", "NoSQL"], "likes" : 100 }
```

接下来移除 title 为“MongoDB”的文档，执行以下操作后，查询会发现两个文档记录均被删除：

```

>db.test.remove({'title': 'MongoDB'})
WriteResult({ 'nRemoved' : 2 })    #删除了两条数据
```

另外，可以设置比较条件，如下操作为删除 price 大于 3 的文档记录：

```

>db.test.remove(
    {
        price:{$gt:3}
    }
)
```

## delete() 方法

官方推荐使用 deleteOne() 和 deleteMany() 方法删除文档，语法格式如下：

db.collection.deleteMany ({})
db.collection.deleteMany ({ status : "A" })
db.collection.delete.One ({ status : "D" })

第一条语句删除集合下所有的文档，第二条语句删除 status 等于 A 的全部文档，第三条语句删除 status 等于 D 的一个文档。