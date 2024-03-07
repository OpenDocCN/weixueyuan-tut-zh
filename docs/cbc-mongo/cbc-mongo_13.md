# MongoDB find()方法：查询数据

> 原文：[`c.biancheng.net/view/6555.html`](http://c.biancheng.net/view/6555.html)

在关系型数据库中，可以实现基于表的各种各样的查询，以及通过投影来返回指定的列，相应的查询功能也可以在 MongoDB 中实现。同时由于 MongoDB 支持嵌套文档和数组，MongoDB 也可以实现基于嵌套文档和数组的查询。

## find() 简介

MongoDB 中查询文档使用 find() 方法。find() 方法以非结构化的方式来显示所要查询的文档， 查询数据的语法格式如下：

>db.collection.find(query, projection)

query 为可选项，设置查询操作符指定查询条件；projection 也为可选项，表示使用投影操作符指定返回的字段，如果忽略此选项则返回所有字段。

查询 test 集合中的所有文档时，为了使显示的结果更为直观，可使用 pretty() 方法以格式化的方式来显示所有文档，方法如下：

> db.test.find().pretty()

除了 find() 方法，还可使用 findOne() 方法，它只返回一个文档。

## 查询条件

MongoDB 支持条件操作符，下表为 MongoDB 与 RDBMS 的条件操作符的对比，读者可以通过对比来理解 MongoDB 中条件操作符的使用方法。

MongoDB 与 RDBMS 的查询比较

| 操作符 | 格式 | 实例 | 与 RDBMS where 语句比较 |
| 等于（=） | {<key> : {<value>}} | db.test.find( {price : 24} ) | where price = 24 |
| 大于（>） | {<key> : {$gt : <value>}} | db.test.find( {price : {$gt : 24}} ) | where price > 24 |
| 小于（<） | {<key> : {$lt : <value>}} | db.test.find( {price : {$lt : 24}} ) | where price < 24 |
| 大于等于（>=） | {<key> : {$gte : <value>}} | db.test.find( {price : {$gte : 24}} ) | where price >= 24 |
| 小于等于（<=） | {<key> : {$lte : <value>}} | db.test.find( {price : {$lte : 24}} ) | where price <= 24 |
| 不等于（!=） | {<key> : {$ne : <value>}} | db.test.find( {price : {$ne : 24}} ) | where price != 24 |
| 与（and） | {key01 : value01, key02 : value02, ...} | db.test.find( {name : "《MongoDB 入门教程》", price : 24} ) | where name = "《MongoDB 入门教程》" and price = 24 |
| 或（or） | {$or : [{key01 : value01}, {key02 : value02}, ...]} | db.test.find( {$or:[{name : "《MongoDB 入门教程》"},{price : 24}]} ) | where name = "《MongoDB 入门教程》" or price = 24 |

## 特定类型查询

特定类型查询结果在 test 集合中有以下文档为基础：

```

> db.test.find()
    {"_id" : Objectld("5ba7342c7f9318ea62161351"), "name" : "《MongoDB 教程》", "price" : 24, "tags" : [ "MongoDB", "NoSQL", "database" ], "by": "C 语言中文网"}
    {"_id" : Objectld("5ba747bd7f9318ea62161352"), "name" ： "ava 教程", "price" : 36, "tags" : ["编程语言", "Java 语言", "面向对象程序设计语言"], "by" : "C 语言中文网"}
    {"_id" : Objectld("5ba75a057f9318ea62161356"), "name" : "王二", "age" : null }
```

查询 age 为 null 的字段的语法格式如下：

> db.test.find({age:null})

此语句不仅匹配出 age 为 null 的文档，其他不同类型的文档也会被查出。这是因为 null 不仅会匹配某个键值为 null 的文档，而且还会匹配不包含这个键的文档。

查询数组可使用以下语法格式：

```

> db.test.find(
{
    tags:['MongoDB', 'NoSQL', 'database']
}
)
{"_id" : ObjectId("5ba7342c7f9318ea62161351"), "name": "《MongoDB 教程》", "price" : 24, "tags" : [ "MongoDB", "NoSQL", "database"], "by" : "C 语言中文网"}
```

查询有 3 个元素的数组的代码如下：

```

> db.test.find(
{
    tags:{$size:3}
}
)
{"_id" : Objectld("5baf9b6663ba0fb3ccccle77"), "name" : "《MongoDB 教程》", ''price" : 24, "tags" : ["MongoDB"，"NoSQL", "database"], "by" : "C 语言中文网"}
{"_id" : Objectld ("5baf 9bc763ba0fk>3ccccle78"), "name" : "《Java 教程》", "price" : 36, "tags" : ["编程语言", "Java 语言", "面向对象程序设计语言"], "by" : "C 语言中文网"}
```

查询数组里的某一个值的代码如下：

```

> db.test.find(
{
    tags: "MongoDB"
}
)
{"_id" : Objectld("5baf9b6663ba0fb3ccccle77"), "name" : "《MongoDB 教程》", ''price" : 24, "tags" : ["MongoDB"，"NoSQL", "database"], "by" : "C 语言中文网"}
```

limit() 函数与 SQL 中的作用相同，用于限制查询结果的个数，如下语句只返回 3 个匹配的结果。若匹配的结果不到 3 个，则返回匹配数量的结果：

```

>db.test.find().limit(3)
```

Skip() 函数用于略过指定个数的文档，如下语句略过第一个文档，返回后两个：

```

>db.test.find().skip(1)
```

sort() 函数用于对查询结果进行排序，1 是升序，-1 是降序，如下语句可将查询结果升序显示：

```

>db.test.find().sort({"price" : 1})
```

使用 $regex 操作符来设置匹配字符串的正则表达式，不同于全文检索，使用正则表达式无须进行任何配置。如下所示为使用正则表达式查询含有 MongoDB 的文档：

```

> db.test.find({tags:{$regex:"MongoDB"}})
{"_id" : Objectld("5baf9b6663ba0fb3ccccle77"), "name" : "《MongoDB 教程》", ''price" : 24, "tags" : ["MongoDB"，"NoSQL", "database"], "by" : "C 语言中文网"}
```

## 游标

游标是指对数据一行一行地进行操作，在 MongoDB 数据库中对游标的控制非常简单，只需使用 firid() 函数就可以返回游标。有关游标的方法参见下表。

MongoDB 游标的使用

| 方法名 | 作用 |
| hasNext | 判断是否有更多的文档 |
| next | 用来获取下一条文档 |
| toArray | 将查询结构放到数组中 |
| count | 查询的结果为文档的总数量 |
| limit | 限制查询结果返回数量 |
| skip | 跳过指定数目的文档 |
| sort | 对查询结果进行排序 |
| objsLeftlnBatch | 查看当前批次剩余的未被迭代的文档数量 |
| addOption | 为游标设置辅助选项，修改游标的默认行为 |
| hint | 为查询强制使用指定索引 |
| explain | 用于获取查询执行过程报告 |
| snapshot | 对查询结果使用快照 |

使用游标时，需要注意下面 4 个问题。

1) 当调用 find() 函数时，Shell 并不立即查询数据库，而是等真正开始获取结果时才发送查询请求。

2) 游标对象的每个方法几乎都会返回游标对象本身，这样可以方便进行链式函数的调用。

3) 在 MongoDB Shell 中使用游标输出文档包含两种情况，如果不将 find() 函数返回的游标赋值给一个局部变量进行保存，在默认情况下游标会自动迭代 20 次。如果将 find() 函数返回的游标赋值给一个局部变量，则可以使用游标对象提供的函数进行手动迭代。

4) 使用清空后的游标，进行迭代输出时，显示的内容为空。

游标从创建到被销毁的整个过程存在的时间，被称为游标的生命周期，包括游标的创建、使用及销毁三个阶段。当客户端使用 find() 函数向服务器端发起一次查询请求时，会在服务器端创建一个游标，然后就可以使用游标函数来操作查询结果。

以下三种情况会让游标被销毁。

*   客户端保存的游标变量不在作用域内。
*   游标遍历完成后，或者客户端主动发送终止消息。
*   在服务器端 10 分钟内未对游标进行操作。

以下语句显示使用游标查找所有文档：

```

>var cursor = db.test.find()
>while (cursor.hasNext()){
    var doc = cursor.next();
    print(doc.name);  //把每一条数据都单独拿出来进行逐行的控制
    print(doc);  //将游标数据取出来后，其实每行数据返回的都是一个［object BSON］型的内容
    printjson(doc);  //将游标获取的集合以 JSON 的形式显示
}
```