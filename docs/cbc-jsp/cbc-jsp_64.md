# JDBC API ResultSet 接口（记录集接口）

在 JDBC API 2.0 中，ResultSet 接口有了很大的变化，增加了很多行操作、行定位的新方法，功能也强大了许多，下面我们就详细地对其进行介绍。

#### 1\. 新定义了若干个常数

在 JDBC API 2.0 中，新定义了许多常数用来指定 ResultSet 的类型，如下所示：

public static final int FETCH_FORWARD;
public static final int FETCH_REVERSE;
public static final int FETCH_UNKNOWN;
public static final int TYPE_FORWARD_ONLY;
public static final int TYPE_SCROLL_INSENSITIVE;
public static final int TYPE_SCROLL_SENSITIVE;
public static final int CONCUR_READ_ONLY;
public static final int CONCUR_UPDATABLE;

上述新定义的常数其游标移动的方向等性质如下：

1.  fetch_forward：该常数的作用是指定处理记录集中行的顺序是由前到后，即从第一行开始处理，一直到最后一行。
2.  fetch_reverse：该常数的作用是指定处理记录集中行的顺序是由后到前，即从最后一行开始处理一直到第一行。
3.  fetch_unknown：该常数的作用是不指定处理记录集中行的顺序，由 JDBC 驱动程序和数据库系统决定。
4.  type_forward_only：该常数的作用是指定数据库游标的移动方向是向前，不允许向后移动，即只能使用 ResultSet 接口的 next() 方法，而不能使用 previous() 方法，否则会产生错误。
5.  type_scroll_insensitive：该常数的作用是指定数据库游标可以在记录集中前后移动，并且当前数据库用户获取的记录集对其他用户的操作不敏感。就是说当前用户浏览记录集中的数据的同时，其他用户更新了数据库中的数据，但是当前用户所获取的记录集中的数据不会受到任何影响。
6.  type_scroll_sensitive：该常数的作用是指定数据库游标可以在记录集中前后移动，并且当前数据库用户获取的记录集对其他用户的操作敏感。就是说，当前用户正在浏览记录集，若其他用户的操作使数据库中的数据发生了变化，则当前用户所获取的记录集中的数据也会同步发生变化。这样有可能会导致非常严重的错误，建议慎重使用该常数。
7.  concur_read_only：该常数的作用是指定当前记录集的协作方式（concurrency mode）为只读，一旦使用了这个常数，则不可以更新记录集中的数据。
8.  concur_updatable：该常数的作用是指定当前记录集的协作方式（concurrency mode）为可以更新，一旦使用了这个常数，就可以使用 updateXXX() 等方法更新记录集中的数据。

#### 2\. ResultSet 接口提供了一整套的定位方法

这些定位方法可以在记录集中定位到任意一行，具体有 public boolean absolute(int row)、public boolean relative(int rows) 等方法，如下表所示：

表 1 ResultSet 接口提供的定位方法

| 方法 | 作用 |
| public boolean absolute(int row) | 该方法的作用是将记录集中的某一行设定为当前行，亦即将数据库游标移动到指定的行参数 row 指定的目标行的行号，这是绝对的行号，由记录集的第一行开始计算。 |
| public boolean relative(int rows) | 该方法的作用也是将记录集中的某一行设定为当前行，但是它的参数 rows 表示目标行相对 于当前行的行号。 |
| public boolean first() | 该方法的作用是将当前行定位到数据库记录集的第一行。 |
| public boolean last() | 该方法的作用刚好和 first() 方法相反，是将当前行定位到数据库记录集的最后一行。 |
| public boolean isFirst() | 该方法的作用是检查当前行是否为记录集的第一行。如果是，返回 true，否则返回 false。 |
| public boolean isLast() | 该方法的作用是检查当前行是否为记录集的最后一行。如果是，返回 true，否则返回 false。 |
| public void afterLast() | 该方法的作用是将数据库游标移到记录集的最后，位于记录集最后一行的后面。如果该记录 集不包含任何行，该方法不产生作用。 |
| public void beforeFirst() | 该方法的作用是将数据库游标移到记录集的最前面，位于记录集第一行的前面。如果记录集 不包含任何行，该方法不产生作用。 |
| public boolean isAfterLast() | 该方法检查数据库游标是否处于记录集的最后面。如果是，返回 true，否则返回 false。 |
| public boolean isBeforeFirst() | 该方法检查数据库游标是否处于记录集的最前面。如果是，返回 true，否则返回 false。 |
| public boolean next() | 该方法的作用是将数据库游标向前移动一位，使得下一行成为当前行。当刚刚打开记录集对象 时，数据库游标的位置在记录集的最前面。第一次使用 next() 方法，将会使数据库游标定位到
记录集的第一行；第二次使用 next() 方法，将会使数据库游标定位到记录集的第二行；以此类
推。 |
| public boolean previous() | 该方法的作用是将数据库游标向后移动一位，使得上一行成为当前行。 |

注意，在 public boolean relative(int rows) 方法中，参数 rows 表示目标行相对于当前行的行号，例如当前行是第 3 行，现在需要移动到第 5 行，则既可以使用 absolute() 方法，也可以使用 relative() 方法，代码如下：

```
rs.absolute(5);
```

或者

```
rs.relative(2);
```

其中，rs 代表 ResultSet 接口的实例对象。

又如，当前行是第 5 行，需要移动到第 3 行的代码如下：

```
rs.absolute(3);
```

或者

```
rs.relative(-2);
```

其中，rs 代表 ResultSet 接口的实例对象。

注意，传递给 relative() 方法的参数，如果是正数，那么数据库游标向前移动；如果是负数，那么数据库游标向后移动。

【例 1】使用 ResultSet 接口的方法在记录集中定位到特定的行。dbScroll.jsp 的代码如下：

```
<%--File Name:Application.java--%>
<%@page import="java.sql.*" %>
<%
String url="jdbc:weblogic:mssqlserver4:rainbow:1433";
Connection con;
Statement stmt;
try
{
    Class.forName("weblogic.jdbc.mssqlserver4.Driver ");
}
catch(java.lang.ClassNotFoundException e)
{
    out.print("ClassNotFoundException: ");
    out.println(e.getMessage());
}
try
{
    con=DriverManager .getConnection (url "sa" "");
    stmt=con.createStatement (ResultSet.TYPE_SCROLL_SENSITIVE
    ResultSet.CONCUR_READ_ONLY);
    ResultSet srs=stmt.executeQuery("USE fancy SELECT * FROM goods");
    srs.absolute(4);
    int rowNum=srs.getRow();    //rowNum should be 4
    out.println("rowNum should be 4"+rowNum);
    srs.relative(-3);
    rowNum=srs.getRow();    //rowNum should be 1
    out.println("rowNum should be 1"+rowNum);
    srs.relative(2);
    rowNum=srs.getRow();    //rowNum should be 3
    out.println("rowNum should be 3"+rowNum);
    srs.absolute(1);
    out.println("after last?"+srs.isAfterLast());
    if(!srs.isAfterLast())
    {
        String name=srs.getString("goodsname");
        float price=srs.getFloat("price");
        out.println(name+" "+price);
    }
    srs.afterLast();
    while (srs.previous())
    {
        String name=srs.getString("goodsname");
        float price=srs.getFloat("price");
        out.println(name+" "+price);
    }
    srs.close();
    stmt.close();
    con.close();
}
catch(BatchUpdateException b)
{
    out.println("-----BatchUpdateException-----");
    out.println("SQL State:"+b.getSQL State());
    out.println("Message:"+b.getMessage());
    out.println("Vendor:"+b.getErrorCode());
    out.print("Update counts: ");
    int [] updateCounts=b.getUpdateCounts();
    for(int i=0;i<updateCounts.length;i++)
    {
        out.print(updateCounts[i]+" ");
    }
    out.println("");
}
catch(SQL Exception ex)
{
    out.println("-----SQL Exception-----");
    out.println("SQL State:"+ex.getSQL State());
    out.println("Message:"+ex.getMessage());
    out.println("Vendor:"+ex.getErrorCode());
}
%>
```

dbScroll.jsp 的运行环境是 BEA WebLogic Server,数据库服务器是 Microsoft SQL Server 2010，主机名为 rainbow，数据库服务器侦听端口为 1433，数据库的名称是 fancy，创建 fancy 数据库的 SQL 文件是 db.sql。读者不能使用 JDBC-ODBC 桥驱动程序，否则会出错。

#### 3\. ResultSet 接口添加了对行操作的支持

使用 JDBC API 不仅可以将数据库游标定位到记录集中的特定行，而且还可以使用 ResultSet 接口新定义的一套方法更新当前行的数据。ResultSet 接口对数据库的基本操作方法如下表所示。

表 2 ResultSet 接口对数据库的基本操作方法

| 方法 | 作用 |
| public boolean rowDeleted() | 如果当前记录集的某行被删除了，那么记录集中将会留出一个空位， 调用 rowDeleted() 方法。如果探测到空位的存在那么就返回 true，
如果没有探测到空位的存在就返回 false。 |
| public boolean rowInserted() | 如果当前记录集中插入了一个新行，该方法将返回 true，否则返回 false。 |
| public boolean rowUpdated() | 如果当前记录集的当前行的数据被更新，该方法返回 true，否则返 回 false。 |
| public void insertRow() | 该方法将执行在当前记录集插入一个新行的操作。 |
| public void updateRow() | 该方法将更新当前记录集当前行的数据。 |
| public void deleteRow() | 该方法将删除当前记录集的当前行。 |
| public void updateString(int columnlndex,String x) | 该方法更新当前记录集当前行某列的值，该列的数据类型是 String （指 Java 数据类型是 String，与之对应的 JDBC 数据类型是
VARCHAR 或 NVARCHAR 等）。该方法的参数 columnIndex 指
定所要更新的列的列索引，第一列的列索引是 1，以此类推；第二
个参数 x 代表新的值。这个方法并不执行数据库操作，需要执行
insertRow() 方法或者 updateRow() 方法以后，记录集和数据库中
的数据才能够真正更新。 |
| public void updateString(String columnName,String x) | 该方法和上面介绍的同名方法差不多，不过该方法的第一个参数是 cohmnName，代表需要更新的列的列名而不是 columnIndex。 |

ResultSet 接口中还定义了多个 updateXXX() 方法，都和上面的两个方法类似，由于篇幅的原因，这里不再详细描述。

在数据库的当前记录集插入新行的操作流程如下：

*   调用 moveToInsertRow() 方法。
*   调用 updateXXX() 方法指定插入行各列的值。
*   调用 insertRow() 方法往数据库中插入新的行。

【例 2】实现在数据库中插入新的行（亦即新的记录）。insertRow.jsp 的代码如下：

```
<%@page import="java.sql.*" %>
<%
String url="jdbc:weblogic:mssqlserver4:rainbow:1433";
Connection con;
Statement stmt;
try
{
    Class.forName("weblogic.jdbc.mssqlserver4.Driver ");
}
catch(java.lang.ClassNotFoundException e)
{
    out.println("ClassNotFoundException: ");
    out.println(e.getMessage());
}
try
{
    con=DriverManager.getConnection (url "sa" "");
    stmt=con.createStatement (ResultSet.TYPE_SCROLL_SENSITIVE
    ResultSet.CONCUR_UPDATABLE);
    ResultSet uprs=stmt.executeQuery("USE fancy SELECT * FROM tbuser");
    uprs.moveToInsertRow();
    uprs.updateString("username" "peking");
    uprs.updateString(2 "peking");
    uprs.insertRow();
    uprs.updateString(1 "lijishan");
    uprs.updateString("password" "lijishan");
    uprs.insertRow();
    uprs.beforeFirst();
    out.println("Table tbuser after insertion:");
    while (uprs.next())
    {
        String name=uprs.getString("username");
        String pass=uprs.getString("password");
        out.println("username:"+name+"<br>");
        out.println("password:"+pass+"<br>");
    }
    uprs.close();
    stmt.close();
    con.close();
}
catch(SQL Exception ex)
{
    out.println("SQL Exception:"+ex.getMessage());
}
%>
```

insertRow.jsp 向 fancy 数据库的 tbuser 表插入了两行，亦即两个记录，然后执行数据库查询，检查 INSERT 操作对数据库的影响，insertRow.jsp 程序应用了上面讲述的方法，比较简单，这里就不重复介绍程序中所用到的各个方法了。

更新数据库中某个记录的值（某行的值）的方法如下：

*   定位到需要修改的行（使用 absolute()、relative() 等方法定位）。
*   使用相应的 updateXXX() 方法设定某行某列的新值，XXX 所代表的 Java 数据类型必须可以映射为某列的 JDBC 数据类型，如果希望 rollback 该项操作，需要再调用 updateRow() 方法以前使用的 cancelRowUpdates() 方法，这个方法可以将某行某列的值复原。
*   使用 updateRow() 方法，完成 UPDATE 的操作。

删除记录集中某行（亦即删除某个记录）的方法是定位到需要修改的行（使用 absolute()、relative()等方法定位），使用 deleteRow() 方法删除。

#### 4\. 新的 ResultSet 接口添加了对 SQL 3 数据类型的支持

SQL 3 技术规范中添加了若干个新的数据类型，如 REF、ARRAY 等。

ResultSet 接口扩充了 getXXX() 方法，添加获取数据的 getXXX() 方法有：getARRAY()、getBlob()、getBigDecimal()、getClob()、getRef()，这些方法既可以接收列索引为参数，也可以接收列名（字段名）为参数，这些方法分别返回对应的 Java 对象实例，如 ClobARRAY(JDBC ARRAY)、Blob、BigDecimal、Ref 等，使用起来十分方便。这些方法的用法在下面还会涉及，这里就不再赘述了。

#### 5\. 获取记录集行数的方法

使用 last() 方法，将数据库游标定位到记录集的最后一行。

使用 getRow() 方法，返回记录集最后一行的行索引。该索引就等于记录集所包含记录的个数。