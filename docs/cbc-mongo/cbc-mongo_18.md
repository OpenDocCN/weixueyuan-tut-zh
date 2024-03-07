# Java 操作 MongoDB 数据库（简明版）

> 原文：[`c.biancheng.net/view/6571.html`](http://c.biancheng.net/view/6571.html)

除了通过启动 mongo 进程进如 Shell 环境访问数据库外，MongoDB 还提供了其他基于编程语言的访问数据库方法。MongoDB 官方提供了 Java 和 Python 语言的驱动包，利用这些驱动包可使用多种编程方法来连接并操作 MongoDB 数据库。

使用 Python 语言的读者请转到：Python 操作 MongoDB 数据库

本节将介绍如何设置和使用 MongoDB JDBC 驱动程序，通过 JDBC 实现与 MongoDB 服务端的通信功能，用户可以在此基础上进行各种 Java 程序的开发。

## 安装 Java 语言驱动包

#### 1) Maven 方式

推荐使用 Maven 的方式管理 MongoDB 的相关依赖包，Maven 项目中只需导入如下 pom 依赖包即可：

```

<dependency>
    <groupld>org.mongodb</groupld>
    <artifactld>mongodb-driver</artifact1d>
    <version>3.4</version>
</dependency>
<dependency>
    <groupld>org.mongodb</groupld>
    <artifactld>bson</artifactId>
    <version>3.4</version>
</dependency>
<dependency>
    <groupld>org.mongodb</groupId>
    <artifactId>Mongodb-driver-core</artifactld>
    <version>3.4</version>
</dependency>
```

#### 2) 手动导入

如果手动下载 mongodb-driver，还必须下载其依赖项 bson 和 mongodb-driver-core。在这里需要注意的是，这三个安装包需要配合使用，并且版本必须一致，否则运行时会报错。

首先安装 MongoDB，本节实例为 MongoDB 3.4.2 版本；然后安装 Java 开发工具，本节采用 Eclipse 开发工具。通过 Github 网站下载驱动包，分别为 mongodb-driver-3.4.2.jar、mongodb-driver-core-3.4.2.jar、bson-3.4.2.jar。

用 Eclipse 创建项目，然后导入需要的包，就可以在 Eclipse 中用代码实现 MongoDB 的简易连接。

## 编程实现

#### 1) import 基础类库

若要完成 MongoDB 的增、删、改、查等操作，则需要导入很多类库。这里介绍可能会用到的类库，如连接数据库和建立客户端的类库、数据库集合和文件操作的类库等。

```

import com.mongodb.MongoClient;
import com.mongodb.client.MongoDatabase;
import com.mongodb.dient.MongoCollection;
```

可以根据编程需要添加必要的类库。

#### 2) 连接数据库

若要连接数据库，则需要指定数据库名称。如果数据库不存在，则 MongoDB 会自动创建数据库。如下代码实现了简易的数据库连接：

```

public class App {
    public static void main(String[] args) {
        try {
            //连接 MongoDB 服务器，端口号为 27017
            MongoClient mongoClient = new MongoClient("localhost", 27017);
            //连接数据库
            MongoDatabase mDatabase = mongoClient.getDatabase("test");  //test 可选
            System.out.printin("Connect to database successfully!");
            System.out.printIn("MongoDatabase inof is : "+mDatabase.getName());
        } catch (Exception e) {
            System.err.printIn(e.getClass().getName() + ": " + e.getMessage());
        }
    }
}
```

#### 3) 切换至集合

连接至具体数据库以后，使用以下代码切换到集合，如果集合不存在，则使用如下代码新建集合：

```

MongoCollection collection = database.getCollection("myTestCollection");
```

#### 4) 插入文档

切换至集合后，就可以进行文档的增、删、改、查操作。首先定义文档，并使用 append。方法追加内容，代码如下：

```

Document document = new Document("_id", 1999)
                    .append("title", "MongoDB Insert Demo")
                    .append("description","database")
                    .append("likes", 30)
                    .append("by", "demo point")
                    .append("url", "http://c.biancheng.net/mongodb/");
```

document 为 BSON 类型的文档，实际上为一个列表，每项有两个元素，即字段名和值。

文档定义完成后，再使用 insertOne 方法将此文档插入集合：

```

collection.insertOne(document);
```

如果插入多条数据，需要先定义一个 Document 列表，然后用 add() 方法添加多个 Document 元素，最后用 insertMany() 方法插入，代码如下：

```

List<Document> documents = new ArrayList<Document>();
documents.add(document1);
collection.insertMany(documents);
```

#### 5) 删除文档

使用 delete() 方法删除一个或多个文档，代码如下：

```

collection.deleteOne(document);
collection.deleteMany(new Document ("likes", 30));
```

#### 6) 更新数据

使用 updataOne() 方法更新一条数据或多个数据，代码如下：

```

collection.updataOne (eq ("likes", 30), new Document ("$set", new Document ("likes", 50)));
```

updateOne() 方法中有两个参数，第一个参数表示更新的内容等于 ("likes",30) 的文档，第二个参数为要修改的内容，使用 $set 参数修改当前值，修改的内容为 ("likes", 50)。

#### 7) 查询数据

利用游标类型实现数据的查询和遍历显示，使用游标前需要 import MongoCursor 类库。

```

import com.mongodb.client.MongoCursor;
document Doc = (Document)collection.find(eq("likes", 30)).iterator();
MongoCursor<Document> cursor =collection.find().iterator();
try{
    while (cursor.hasNext()){
        System.out.printin(cursor.next().toJson());
    }
} finally{
    Cursor.close();
}
```

设置 find() 方法的参数为查询条件，参数为比较的 Document 元素。

#### 8) 其他方法

删除数据库或集合，代码如下：

```

mDatabase.drop();
collection.drop();
```

关闭客户端连接，代码如下：

```

mongoClient.close();
```