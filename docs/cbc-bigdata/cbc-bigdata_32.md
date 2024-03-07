# HBase 常用 Java API

> 原文：[`c.biancheng.net/view/3599.html`](http://c.biancheng.net/view/3599.html)

本节介绍与 HBase 数据存储管理相关的 Java API（基于 HBase 版本 1.2.3）。

## HBase 的常用 Java API

HBase 主要包括 5 大类操作：HBase 的配置、HBase 表的管理、列族的管理、列的管理、数据操作等。

**1）org.apache.hadoop.hbase.HBaseConfiguration**

HBaseConfiguration 类用于管理 HBase 的配置信息，使用举例如下。

static Configuration cfg = HBaseConfiguration.create();

**2）org.apache.hadoop.hbase.client.Admin**

Admin 是 Java 接口类型，不能直接用该接口来实例化一个对象，而是必须通过调用 Connection.getAdmin() 方法，来调用返回子对象的成员方法。该接口用来管理 HBase 数据库的表信息。它提供的方法包括创建表，删除表，列出表项，使表有效或无效，以及添加或删除表列族成员等。

创建表使用的例子如下。

```

Configuration configuration = HBaseConfiguration.create();
Connection connection = ConnectionFactory.createConnection(configuration);
Admin admin = connection.getAdmin();
if(admin.tableExists(tableName)) {//如果存在要创建的表，那么先删除，再创建
    admin.disableTable(tableName);
    admin.deleteTable(tableName);
}
admin.createTable(tableDescriptor);
admin.disableTable(tableName);
HColumnDescriptor hd = new HColumnDescriptor(columnFamily);
admin.addColumn(tableName,hd);
```

**3）org.apache.hadoop.hbase.HTableDescriptor**

HTableDescriptor 包含了表的详细信息。创建表时添加列族使用的例子如下。

```

HTableDescriptor tableDescriptor = new HTableDescriptor (tableName);// 表的数据模式
tableDescriptor. addFamily (new HColumnDescriptor("name"));// 增加列族
tableDescriptor.addFamily(new HColumnDescriptor("age"));
tableDescriptor.addFamily(new HColumnDescriptor("gender"));
admin.createTable(tableDescriptor);
```

**4）org.apache.hadoop.hbase.HColumnDescriptor**

HColumnDescriptor 类维护着关于列族的信息，如版本号、压缩设置等。它通常在创建表或者为表添加列族的时候使用。列族被创建后不能直接修改，只能通过删除然后重新创建的方式。列族被删除的时候，列族里面的数据也会同时被删除。创建表时添加列族的例子如下。

```

HTableDescriptor tableDescriptor = new HTableDescriptor(tableName);// 表的数据模式
HColumnDescriptor hcd = HColumnDescriptor("name".getBytes.());// 构造列族
hcd.setValue("firstName".getBytes(),"John".getBytes());//给列族添加列
hcd.setValue("lastName".getBytes(),"Bates".getBytes());// 给列族添加列
tableDescriptor.addFamily(hcd);//把列族加入表描述
```

**5）org.apache.hadoop.hbase.client.Table**

Table 是 Java 接口类型，不可以用 Table 直接实例化一个对象，而是必须通过调用 connection.getTable() 的一个子对象，来调用返回子对象的成员方法。这个接口可以用来和 HBase 表直接通信，可以从表中获取数据、添加数据、删除数据和扫描数据。例子如下。

```

Configuration configuration = HBaseConfiguration.create();
Connection connection = ConnectionFactory.createConnection(configuration);
Table table = connection.getTable();
ResultScanner scanner = table.getScanner(family);
```

**6）org.apache.hadoop.hbase.client.Put**

Put 类用来对单元执行添加数据操作。给表里添加数据的例子如下。

```

Configuration configuration = HBaseConfiguration.create();
Connection connection = ConnectionFactory.createConnection(configuration);
Table table = connection.getTable();
Put put = new Put ("*1111".getBytes());//—个 Put 代表一行数据，行键为构造方法中传入的值
put.addColumn("name".getBytes(),null,"Ghander".getBytes());//本行数据的第一列
put.addColumn("age".getBytes(),null,"20".getBytes());// 本行数据的第二列
put.addColumn("gender".getBytes(),null,"male".getBytes());// 本行數据的第三列
put.add("score".getBytes(),"Math".getBytes(),"99".getBytes());// 本行数据的第四列
table.put(put);
```

**7）org.apache.hadoop.hbase.client.Get**

Get 类用来获取单行的数据。获取指定单元的数据的例子如下。

```

Configuration configuration = HBaseConfiguration.create();
Connection connection = ConnectionFactory.createConnection(configuration);
Table table = connection.getTable();
Get g = new Get(rowKey.getBytes());
Result rs = table.get(g);
```

**8）org.apache.hadoop.hbase.client.Result**

Result 类用来存放 Get 或 Scan 操作后的查询结果，并以 <key,value〉的格式存储在映射表中。
获取指定单元的数据的例子如下。

```

Configuration configuration = HBaseConfiguration.create();
Connection connection = ConnectionFactory.createConnection(configuration);
Table table = connection.getTable();
Get g = new Get(rowKey.getBytes());
Result.rs = table.get(g);
for (KeyValue kv : rs.raw()) {
    System.out.printIn("rowkey:"+new String(kv.getRow()));
    System.out.printIn("Column Family:"+new String(kv.getFamily()));
    System.out.printIn("Column :"+ new String(kv.getQualifier()));
    System.out.printIn("value :"+ new String(kv.getValue()));
}
```

**9）org.apache.hadoop.hbase.client.Scan**

Scan 类可以用来限定需要查找的数据，如版本号、起始行号、终止行号、列族、列限定符、返回值的数量的上限等。设置 Scan 的列族、时间戳的范围和每次最多返回的单元数目的例子如下。

```

Scan scan = new Scan();
scan.addFamily(Bytes.toBytes("columnFamily1"));
scan.setTimeRange(1,3);
scan.setBatch(1000);
```

**10）org.apache.hadoop.hbase.client.ResultScanner**

ResultScanner 类是客户端获取值的接口，可以用来限定需要查找的数据，如版本号、起始行号、终止行号、列族、列限定符、返回值的数量的上限等。获取指定单元的数据的例子如下。

```

Scan scan = new Scan();
scan.addColumn(Bytes.toBytes("columnFamily1"),Bytes.toBytes("column1")); 
scan.setTimeRange(1,3);
scan.setBatch(10);
Configuration configuration = HBaseConfiguration.create();
Connection connection = ConnectionFactory.createConnection(configuration);
Table table = connection.getTable();
try {
    ResultScanner resuitScanner = table.getScanner(scan);
    Result rs = resultScanner.next();
    for (; rs != null;rs = resultScanner.next()){
        for (KeyValue kv : rs.list()){
            System.out.printIn("--------------");
            System.out.printIn("rowkey:"+ new String(kv.getRow()));
            System.out.printIn("Column Family: "+ new String(kv.getFamily()));
            System.out.printIn("Column :" + new String(kv.getQualifier ()));
            System.out.printIn("value :"+ new String(kv.getValue()));
        }
    }
} catch (IOException e) {
    e.printStackTrace();
}
```