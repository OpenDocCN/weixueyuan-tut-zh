# MongoDB update()和 save()方法：更新或修改数据

> 原文：[`c.biancheng.net/view/6553.html`](http://c.biancheng.net/view/6553.html)

MongoDB 使用 update() 和 save() 方法来更新（修改）集合中的文档。

## update() 方法

MongoDB update() 更新文档的基本语法如下:

db.collection.update(
    <query>,
    <update>,
    {
        upsert,
        multi,
        writeConcern,
        collation
    }
)

参数说明：

*   <query＞：参数设置查询条件。
*   <update＞：为更新操作符。
*   upsert：为布尔型可选项，表示如果不存在 update 的记录，是否插入这个新的文档。true 为插入；默认为 false，不插入。
*   multi：也是布尔型可选项，默认是 false，只更新找到的第一条记录。如果为 true，则把按条件查询出来的记录全部更新。
*   writeConcem：表示出错级别。
*   collation：指定语言。

例如，插入多条数据后，使用 update 进行更改，代码如下：

```

db.test.insertMany ([
    { item : "card"，qty : 15 },
    { item : "envelope", qty: 20 },
    { item : "stamps", qty: 30 }
]);
```

将 item 为 card 的数量 qty 更正为 35，代码如下：

```

db.test.update(
{
    item : "card"
},
{
    $set: {qty: 35}
}
```

collation 特性允许 MongoDB 的用户根据不同的语言定制排序规则，在 MongoDB 中字符串默认当作一个普通的二进制字符串来对比。而对于中文名称，通常有按拼音顺序排序的需求，这时就可以通过 collation 来实现。

创建集合时，指定 collation 为 zh，按 name 字段排序时，则会按照 collation 指定的中文规则来排序，代码如下：

```

db.createCollection ("person", {collation: {locale: "zh" }})    //创建集合并指定语言
db.person.insert ({name: ”张三”}）
db.person.insert ({name:"李四”}）
db.person.insert ({name: ”王五"}）
db.person.insert ({name: ”马六”}）
db.person.insert ({name:"张七"})
db.person.find().sort({name: 1}) //查询并排序
//查询返回结果
{ "_id" : Objectld ("586b995d0cec8d86881cffae") , "name": "李四" }
{ "_id" : Objectld ("586b995d0cec8d8 6881cffb0") , "name" : "马六" }.
{ "_id" : Objectld ("586b995d0cec8d86881cffaf"), "name": "王五" }
{ "_id" : Objectld ("586b995d0cec8d86881cffb1"), "name": "张七" }
{ "_id" : Objectld ("586b995d0cec8d86881cffad"), "name" : "张三" }
```

## save() 方法

MongoDB 另一个更新（修改）文档的方法是 save()，语法格式如下：

db.collection.save ( obj )

obj 代表需要更新的对象，如果集合内部已经存在一个与 obj 相同的“_id”的记录，Mongodb 会把 obj 对象替换为集合内已存在的记录；如果不存在，则会插入 obj 对象。

如下代码会先保存一个 _id 为 100 的记录，然后再执行 save，并对当前已经存在的数据进行修改：

```

db.products.save( { _id: 100, item: "watern, qty: 30 })
db.products.save( { _id : 100, item : "juice" })
```

如果使用 insert 插入记录，若新增数据的主键已经存在，则会抛出 DuplicateKeyException 异常提示主键重复，不保存当前数据。