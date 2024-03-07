# Python 操作 MongoDB 数据库（简明版）

> 原文：[`c.biancheng.net/view/6572.html`](http://c.biancheng.net/view/6572.html)

除了通过启动 mongo 进程进如 Shell 环境访问数据库外，MongoDB 还提供了其他基于编程语言的访问数据库方法。MongoDB 官方提供了 Java 和 Python 语言的驱动包，利用这些驱动包可使用多种编程方法来连接并操作 MongoDB 数据库。

使用 Java 语言的读者请转到：Java 操作 MongoDB 数据库

通过 Python 3.x 访问 MongoDB，需要借助开源驱动库 pymongo（由 MongoDB 官方提供）。pymongo 驱动程序可以直接连接 MongdoDB 数据库，然后对数据库进行操作。

安装 pymongo 驱动可使用 pip 方式：

pip install pymongo

## 建立连接

#### 1) 模块引用

Python 驱动库连接 MongoDB 比较简单，而且同时支持自动的故障修复，即连接时出现故障会自动重新连接。

在 Python 脚本中连接 MongoDB 首先要导入需要的 pymongo 库：

import pymongo

然后使用 MongoClient 对象创建与数据库服务器的连接：

```

Client = MongoClient(host= '10.90.9.101', port=27017)
```

使用上面的代码片段，通过指定 host 和 port 连接到我们在《将 MongoDB 部署到分布式集群（实操）》一节中部署的集群中的路由服务器。

当然也可连接具体的 mongod 服务器或副本集：

```

Client=MongoClient(host='10.90.9.102',port=27018)
Client=MongoClient(host='10.90.9.101: 27018, 10.90.9.102: 27018,10.90.9.103: 27018')
```

#### 2) 访问数据库

创建 MongoClient 实例后，就可以访问服务器中的任何数据。如果要访问一个数据库，可以将其当作属性一样进行访问：

```

db = client.myDB
```

也可以使用函数方式访问，如果不存在数据库，则系统会自动创建数据库：

```

db = conn.get_database("myDB")
```

## 集合操作

#### 1) 插入文档

在数据库中存储数据时，首先指定使用的集合，然后使用集合的 insert_one() 方法插入文档，如下代码定义了 post_data 的 JSON 文档：

```

coll = db.get_collection("myCollection")
post_data = {
    '_id' : '10'
    'item' : 'book1',
    'qty' : 18 }
result = coll.insert_one(post_data)
```

可以使用 insert_one() 同时插入多个文档，将多个文档添加到数据库中，还可以使用方法 insert_many()。此方法的参数可以是如下的 list 列表：

```

post_1 = {
    '_id' : '11',
    'item1' : 'book1',
    'qty' : 18
}
post_2 = {
    '_id' : '12',
    'item1' : 'book1',
    'qty' : 18
}
post_3 = {
    '_id' : '13',
    'item1' : 'book1',
    'qty' : 18
}
new result = coll.insert_many([post_1, post_2, post_3])
```

#### 2) 检索文档

检索文档可以使用 find_one() 方法，例如，可使用如下语句找到 item 为 book 的记录：

```

find_post = coll.find_one({'item' : 'book'})
```

如果需要查询多条记录，则可以使用 find() 方法，代码如下：

```

find_posts = coll.find({'item' : 'book1'})
```

#### 3) 更新数据

可以使用 update() 方法更新数据，只需指定更新的条件和更新后的数据即可：

```

condition = {'item' : 'book'}
pty{'$set' : {'pty' : 22}}
result = col.update(condition, pty)
```

#### 4) 删除数据

删除操作比较简单，直接调用 remove() 方法指定删除的条件即可，符合条件的所有数据均会被删除，代码如下:

```

result = coll.remove({'item' : 'book'})
```

另外，还可以使用 delete_one() 和 delete_many() 方法，示例如下:

```

result = coll.delete_one({'item' : 'book'})
resuIt = collection.delete_many({'item' : book1})
```