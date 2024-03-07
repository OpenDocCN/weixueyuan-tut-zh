# MongoDB 创建和查看数据库

> 原文：[`c.biancheng.net/view/6549.html`](http://c.biancheng.net/view/6549.html)

MongoDB 将 BSON 文档（即数据记录）存储在集合中，数据库包含文档集合。在 MongoDB 里面存在数据库的概念，但没有模式，保存数据的结构是 BSON 结构，只不过在进行一些数据处理的时候才会使用 MongoDB 自己的操作。

MongoDB 自带了一个功能强大的 JavaScript Shell，可以用于管理或操作 MongoDB。

MongoDB 数据库初始安装完成后，默认的数据库是 test，学习时可以在默认 test 数据库上进行各种练习操作。当然在实际的操作过程中需要创建很多实例，因此，用户需要掌握自定义数据库名称的基本规则。

## MongoDB 数据库的命名规则

MongoDB 数据库的命名要符合 UTF-8 标准的字符串，同时要遵循下表所示的注意事项。

MongoDB 数据库命名的注意事项

| 序号 | 注意事项 |
| 1 | 不能是空串 |
| 2 | 不得含有 /、\、?、$、空格、空字符等，基本只能使用 ASCII 中的字母和数字 |
| 3 | 区分大小写，建议全部小写 |
| 4 | 名称最多为 64 字节 |
| 5 | 不得使用保留的数据库名，如：admin、local、config |

注意：数据库最终会成为文件，数据库名就是文件的名称。

*   由于数据库名称在 MongoDB 中不区分大小写，因此数据库名称不能仅仅区别于字符。
*   对于在 Windows 上运行的 MongoDB，数据库名称不能包含以下字符：/、\、“、$、*、＜ ＞、：、|、? 。
*   对于在 UNIX 和 Linux 系统上运行的 MongoDB，数据库名称不能包含以下字符：/、\、。、"、$。
*   虽然 UTF-8 可以提供很多国家的语言的命名格式，在 MongoDB 数据库命名时也可以使用汉字作为数据库名，但是最好尽量采用英文字母、数字、字符等为主的命名格式。

如下命名格式是正确的：myDB、my_NewDB、myDB12。

以下命名格式则不被 MongoDB 接受：.myDB、/123。

#### 保留数据库

MongoDB 系统保留的数据库如表所示。

保留数据库

| 库名 | 作用 |
| admin | 权限数据库，添加用户到该数据库中，该用户会自动继承数据库的所有权限 |
| local | 数据库中的数据永远不会被复制 |
| config | 分片时，config 数据库在内部使用，保存分子信息 |
| test | 默认数据库，可以用来做各种测试等 |

## 创建数据库

MongoDB 使用 use 命令创建数据库，如果数据库不存在，MongoDB 会在第一次使用该数据库时创建数据库。如果数据库已经存在则连接数据库，然后可以在该数据库进行各种操作。

use myDB

## 查看数据库

MongoDB 使用 show 命令查看当前数据库列表，代码如下:

```

>show dbs        //可以在任意当前数据库上执行该命令
admin 0.000GB    //保留数据库，admin
myDB 0.000GB     //自定义数据库，myDB,该数据库里已经插入记录，没有记录的自定义数据库不会显示 
local 0.000GB    //保留数据库，local
test 0.000GB     //保留数据库，test
```

MongoDB 默认的数据库为 test，如果没有创建新的数据库，集合将存储在 test 数据库中。如果自定义数据库没有插入记录，则用户在查看数据库时是不会显示的，只有插入数据的数据库才会显示相应的信息。

## 统计数据库信息

MongoDB 使用 stats() 方法查看某个数据库的具体统计信息，注意对某个数据库进行操作之前，一定要用 use 切换至数据库，否则会出错，代码如下：

```

>use test              //选择执行的 test 数据库
switched to db test    //use 执行后返回的结果
> db. stats ()         //统计数据信息
{ 
    "db" : "test",     //数据库名
    "collections" : 0, //集合数量
    "views" : 0,
    "objects" : 0,     //文档数量
    "avgObjSize" : 0,  //平均每个文档的大小
    "dataSize" : 0,    //数据占用空间大小，不包括索引，单位为字节
    "storageSize" : 0, //分配的存储空间
    "nuinExtents" : 0, //连续分配的数据块
    "indexes" : 0,     //索引个数
    "indexsize" : 0,   //索引占用空间大小
    "fileSize" : 0,    //物理存储文件的大小
    "ok" : 1 
}
```

## 删除数据库

MongoDB 使用 dropDatabase() 方法删除数据库，代码如下：

```

>db.dropDatabase ()    //删除当前数据库
{ ndropped" : "myDBn Jok" : 1}    //显示结果删除成功
```

## 查看集合

MongoDB 使用 getCollectionNames() 方法查询当前数据库下的所有集合，代码如下：

```

>use test
>db.getCollectionNames ()    //查询当前数据下所有的集合名称
```