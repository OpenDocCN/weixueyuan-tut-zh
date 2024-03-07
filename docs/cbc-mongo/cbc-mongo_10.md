# MongoDB insert()方法：插入数据

> 原文：[`c.biancheng.net/view/6552.html`](http://c.biancheng.net/view/6552.html)

要将数据插入 MongoDB 集合中，可以使用 MongoDB 的 insert() 方法，同时 MongoDB 针对插入一条还是多条数据，提供了更可靠的 insertOne() 和 insertMany() 方法。

MongoDB 向集合里插入记录时，无须事先对数据存储结构进行定义。如果待插入的集合不存在，则插入操作会默认创建集合。

在 MongoDB 中，插入操作以单个集合为目标，MongoDB 中的所有写入操作都是单个文档级别的原子操作。

向集合中插入数据的语法如下：

```

db.collection.insert(
<document or array of documents>,
{
    writeConcern: <document>,    //可选字段
    ordered: <boolean>    //可选字段
    }
)
```

db 为数据库名，如当前数据库名为“test”，则用 test 代替 db，collection 为集合名，insert() 为插入文档命令，三者之间用连接。

参数说明：

*   <document or array of documents> 参数表示可设置插入一条或多条文档。
*   writeConcern:<document> 参数表示自定义写出错的级别，是一种出错捕捉机制。
*   ordered:<boolean> 是可选的，默认为 true。
    *   如果为 true，在数组中执行文档的有序插入，并且如果其中一个文档发生错误，MongoDB 将返回而不处理数组中的其余文档；
    *   如果为 false，则执行无序插入，若其中一个文档发生错误，则忽略错误，继续处理数组中的其余文档。

插入不指定 _id 字段的文档的代码如下：

```

> db.test.insert( { item : "card", qty : 15 })
```

在插入期间，mongod 将创建 _id 字段并为其分配唯一的 Objectld 值，这里的 mongod 是一个 MongoDB 服务器的实例，也就是 MongoDB 服务驻扎在计算机上的进程。

查看集合文档的代码如下：

```

> db.test.find()
{"_id":Objectlid("5bacac84bb5e8c5dff78dc21"), "item":"cardn, "qty":15 }
```

这些 Objectld 值与执行操作时的机器和时间有关，因此，用户执行这段命令后的返回值与示例中的值是不同的。

插入指定 _id 字段的文档，值 _id 必须在集合中唯一，以避免重复键错误，代码如下：

```

> db.test.insert(
    { _id: 10, item: "box", qty: 20 }
)
> db.test.find()
{ "_id" : 10, "item" : "box" , "qty": 20 }
```

可以看到新插入文档的 id 值为设置的 id 值。

插入的多个文档无须具有相同的字段。例如，下面代码中的第一个文档包含一个 _id 字段和一个 type 字段，第二个和第三个文档不包含 _id 字段。因此，在插入过程中，MongoDB 将会为第二个和第三个文档创建默认 _id 字段，代码如下：

```

db.test.insert(
    [
        { _id: 11, item: "pencil", qty: 50, type: "no.2" },
        { item: "pen", qty: 20 },
        { item: "eraser", qty: 25 }
    ]
)
```

查询验证，可以看到在 _id 插入期间，系统自动为第二、第三个文档创建了字段，代码如下：

```

> db.test.find()
{ "_id" : 11, "item" : "pencil", "qty" : 50, "type" : "no.2" }
{ "_id" : Objectld("5bacf31728b746e917e06b27"), "item" : "pen"， "qty" : 20 }
{ "_id" : Objectld("5bacf31728b746e917e06b28"), "item" : "eraser", "qty" : 25 }
```

用变量方式插入文档，代码如下：

```

> document= ({ name: "c 语言", price: 40 })    //document 为变量名.
> db.test.insert(document)
```

有序地插入多条文档的代码如下：

```

> db.test.insert([
        {_id:10, item:"pen", price:"20" },
        {_id:12, item:"redpen", price: "30" },
        {_id:11, item:"bluepen", price: "40" }
    ],
    {ordered:true}
)
```

在设置 ordered:true 时，插入的数据是有序的，如果存在某条待插入文档和集合的某文档 _id 相同的情况，_id 相同的文档与后续文档都将不再插入。在设置 ordered:false 时，除了出错记录（包括 _id 重复）外其他的记录继续插入。

MongoDB 3.2 更新后新增以下两种新的文档插入命令如下：

db.collection.insertone ()
db.collection.insertMany()

使用 insertOne() 插入一条文档的代码如下：

```

db.test.iusertone( { item: "card", qty: 15 } );
```

使用 insertMany() 插入多条文档的代码如下：

```

db.test.insertMany([
    { item: "card", qty: 15 },
    { item: "envelope", qty: 20 },
    { item: "stamps", qty:30 }
]);
```