# 利用 JDBC 发送 SQL 语句

Statement 对象用于将 SQL 语句发送到数据库。实际上有三种 Statement 对象，它们都作为在给定连接上执行 SQL 语句的包容器：Statement、PreparedStatement（它从 Statement 继承而来）和 CallableStatement（它从 PreparedStatement 继承而来）。

以上三种 Statement 对象都专用于发送特定类型的 SQL 语句：

*   Statement 对象用于执行不带参数的简单 SQL 语句；
*   PreparedStatement 对象用于执行带或不带 IN 参数的预编译 SQL 语句；
*   CallableStatement 对象用于执行对数据库己存储过程的调用；

Statement 接口提供了执行语句和获取结果的基本方法；PreparedStatement 接口添加了处理 IN 参数的方法；而 CallableStatement 添加了处理 OUT 参数的方法。

#### 1\. 创建 Statement 对象

建立到特定数据库的连接之后，就可用该连接发送 SQL 语句了。Statement 对象用 Connection 的方法 createStatement 创建，如下列代码段所示：

```
Connection con=DriverManager.getConnection(url,"sunny","");
Statement stmt=con.createStatement();
```

为了执行 Statement 对象，被发送到数据库的 SQL 语句将被作为参数提供给 Statement 的方法：

```
ResultSet rs=stmt.executeQuery("SELECT a,b,c FROM Table2");
```

#### 2\. 使用 Statement 对象执行语句

Statement 接口提供了三种执行 SQL 语句的方法：executeQuery、executeUpdate 和 execute。使用哪一个方法由 SQL 语句所产生的内容决定。

executeQuery 方法用于产生单个结果集的语句，例如 SELECT 语句。executeUpdate 方法用于执行 INSERT、UPDATE 或 DELETE 语句以及 SQL DDL（数据定义语言）语句，例如 CREATE TABLE 和 DROP TABLE。

INSERT、UPDATE 或 DELETE 语句的效果是修改表中零行或多行中的一列或多列。executeUpdate 的返回值是一个整数，指示受影响的行数（即 更新计数）。对于 CREATE TABLE 或 DROP TABLE 等不操作行的语句，executeUpdate 的返回值总为零。

执行语句的所有方法都将关闭所调用的 Statement 对象的当前打开结果集（如果存在）。这意味着在重新执行 Statement 对象之前，需要完成对当前 ResultSet 对象的处理。应注意，继承了 Statement 接口中所有方法的 PreparedStatement 接口都有自己的 executeQuery、executeUpdate 和 execute 方法。

Statement 对象本身不包含 SQL 语句，因而必须给 Statement.execute 方法提供 SQL 语句作为参数。PreparedStatement 对象并不需要 SQL 语句作为参数提供给这些方法，因为它们己经包含预编译 SQL 语句。

CallableStatement 对象继承这些方法的 PreparedStatement 形式。对于这些方法的 PreparedStatement 或 CallableStatement 版本，使用查询参数将拋出 SQL Exception。

#### 3\. 语句完成

当连接处于自动提交模式时，其中所执行的语句在完成时将自动提交或还原。语句在已执行且所有结果返回时，即认为己完成。对于返回一个结果集的 executeQuery 方法，在检索完 ResultSet 对象的所有行时该语句完成。对于方法 executeUpdate，当它执行时语句即完成。但在少数调用方法 execute 的情况中，在检索所有结果集或它生成的更新计数之后语句才完成。

有些 DBMS 将已存储过程中的每条语句视为独立的语句；而另外一些则将整个过程视为一个复合语句。在启用自动提交时，这种差别就变得非常重要，因为它会影响什么时候调用 commit 方法。在前一种情况中，每条语句单独提交；在后一种情况中，所有语句同时提交。

#### 4\. 关闭 Statement 对象

Statement 对象将由 Java 垃圾收集程序自动关闭。而作为一种好的编程风格，应在不需要 Statement 对象时显式地关闭它们。这将立即释放 DBMS 资源，有助于避免潜在的内存问题。

#### 5\. 使用 execute 方法

execute 方法应该仅在语句能返回多个 ResultSet 对象、多个更新计数或 ResultSet 对象与更新计数的组合时使用。

当执行某个已存储过程或动态执行未知 SQL 字符串（即应用程序程序员在编译时未知）时，有可能出现多个结果的情况，尽管这种情况很少见。例如，用户可能执行一个已存储过程，并且该已存储过程可执行更新，然后执行选择，再进行更新，再进行选择。通常使用已存储过程的人应知道它所返回的内容。

因为方法 execute 处理非常规情况，所以获取其结果需要一些特殊处理并不足为怪。

例如，假定已知某个过程返回两个结果集，则在使用方法 execute 执行该过程后，必须调用方法 getResultSet 获得第一个结果集，然后调用适当的 getXXX 方法获取其中的值。要获得第二个结果集，需要先调用 getMoreResults 方法，然后再调用 getResultSet 方法。

如果已知某个过程返回两个更新计数，则首先调用方法 getUpdateCount，然后调用 getMoreResults，并再次调用 getUpdateCount。

若不知道返回内容，则情况更为复杂。如果结果是 ResultSet 对象，则方法 execute 返回 true；如果结果是 Javaint，则返回 false。如果返回 int，则意味着结果是更新计数或执行的语句是 DL 命令。

在调用方法 execute 之后要做的第一件事是调用 getResultSet 或 getUpdateCount。调用方法 getResultSet 可以获得两个或多个 ResultSet 对象中的第一个对象；调用方法 getUpdateCount 可以获得两个或多个更新计数中的第一个更新计数的内容。

当 SQL 语句的结果不是结果集时，getResultSet 方法将返回 null。这可能意味着结果是一个更新计数或没有其他结果。在这种情况下，判断 null 真正含义的唯一方法是调用 getUpdateCount 方法，它将返回一个整数。这个整数为调用语句所影响的行数；如果为 -1 则表示结果是结果集或没有结果。如果 getResultSet 方法已返回 null（表示结果不是 ResultSet 对象），则返回值 -1 表示没有其他结果。也就是说，当下列条件为真时表示没有结果（或没有其他结果）：

((stmt.getResultSet()==null)&&(stmt.getUpdateCount()==-1))

如果己经调用方法 getResultSet 并处理了它返回的 ResultSet 对象，则有必要调用方法 getMoreResults 以确定是否有其他结果集或更新计数。如果 getMoreResults 返回 true，则需要再次调用 getResultSet 来检索下一个结果集。如上所述，如果 getResultSet 返回 null，则需要调用 getUpdateCount 来检查 null 是表示结果为更新计数还是表示没有其他结果。

当 getMoreResults 返回 false 时，表示该 SQL 语句返回一个更新计数或没有其他结果。因此需要调用方法 getUpdateCount 来检查它是哪一种情况。在这种情况下，当下列条件为真时表示没有其他结果：

((stmt.getMoreResults()==false)&&(stmt.getUpdateCount()==-1))

下面的代码演示了用来确认已访问调用方法 execute 所产生的全部结果集和更新计数的一种方法：

```
stmt.execute(queryStringWithUnknownResults);
while(true)
{
    introwCount=stmt.getUpdateCount();
    if(rowCount>0)
    {    //它是更新计数
        System.out.println(nRows changed=u+count);
        stmt.getMoreResults();
        continue;
    }
    if(rowCount==0)
    {    //DDL 命令或 0 个更新
        System.out.println("No rows changed or statement was DDL command11);
```