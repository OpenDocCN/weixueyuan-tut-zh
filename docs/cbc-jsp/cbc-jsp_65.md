# JDBC RowSet 接口

RowSet 接口是 RowSet 包中最重要的接口。RowSet 接口为 JDBC API 添加了对 JavaBeans(TM) 组件模型的支持，在一个可视化的 JavaBean 开发环境中，使用 RowSet 对象可以像使用 JavaBean 组件那样方便。

RowSet 对象可以在设计时被创建和指定属性，在程序的运行时被执行，RowSet 接口定义了一套 JavaBean 组件的属性方法，RowSet 实例对象可以使用 setXXX() 方法指定属性以便和数据库建立连接。

RowSet 接口支持 JavaBeans 的事件模型。允许同一个应用程序的其他组件在运行时刻，并且 RowSet 接口的实例对象发生了某种变化/事件的情况下（例如记录集的数据库游标向前移动了一位），调用 RowSet 接口的 setXXX() 方法，修改 RowSet 对象的属性。

RowSet 接口继承自 java.sql.ResultSet 接口，它不但具有 ResultSet 接口强大的记录集功能，而且比 ResultSet 接口更容易使用。下面我们就来介绍如何使用 RowSet 接口。

#### 1\. 创建 RowSet 接口的实例对象

在 JDBCAPI 中，ResultSet 接口的实例对象一般不是调用构造函数直接创建，而是由其他方法的返回结果初始化。例如，Statement 接口的 executeQuery() 方法的返回结果就是 ResultSet 接口的实例对象。RowSet 接口的创建方法如下：

RowSet rs=new RowSet();

#### 2\. 设定 RowSet 对象的数据库连接属性

创建了 RowSet 接口的实例对象以后，必须设定 RowSet 对象的数据库连接属性，以便 RowSet 对象可以和数据库服务器建立连接，进而向数据库发送 SQL 语句访问数据库中的数据，并获取结果记录集。

RowSet 接口定义了下表所示的一些方法（仅列出比较重要的方法），用以指定数据库连接属性，如用户名、访问密码、数据源 URL 等属性。

表 1 RowSet 接口定义的重要方法

| 方法 | 作用 |
| public void setConcurrency(int concurrency) | 该方法可以指定 RowSet 对象和数据库建立的连接的协同级别，通俗地讲， 就是指 RowSet 对象所包含的结果记录集的数据是否可以更新，方法参数
concurrency 是一个整型常数。这些常数在 ResultSet 接口中被定义，
RowSet 接口继承自 ResultSet 接口，自然可以使用这些预定义的常数。 |
| public void setDataSourceName (java.lang.String name)* | RowSet 接口的实例对象可以使用这个方法查询（lookup）特定的（由参数 name 指定）、存在于 JNDI 命名服务（JNDI naming service）中的
JDBCDataSource 对象。 |
| public void setMaxRows(int max) | 该方法可以指定 RowSet 对象中所包含的记录集的最大记录数。 |
| public void setPassword(java.lang.String password) | 该方法可以指定 RowSet 对象和数据库建立连接所需的用户密码。 |
| public void setReadOnly(Boolean value) | 该方法指定 RowSet 对象中所包含的记录集是否只读，方法参数为布尔值， 如果为 true，那么结果集只读；反之亦然。 |
| public void setTransactionIsolation(int level) | 该方法指定事务处理的隔离级别。 |
| public void setType(int type) | RowSet 对象可以使用此方法指定记录集游标的类型。例如，可以前后移动 的数据库游标，或者是仅仅可以往前移动的游标。方法参数 type 是一个整
型常数，这些常数在 ResultSet 接口中定义。 |
| public void setTypeMap(java.util.Map map) | RowSet 对象可以使用此方法指定当前 RowSet 对象与数据库连接时所使用 的映射地图。 |
| public void setURL(java.lang.String URL) | 该方法用于指定 RowSet 对象需要与之建立连接的数据源的 JDBC URL 地 址。例如 JDBCodbc test |
| public void setUsemame(java.lang.String name) | 该方法用于指定 RowSet 对象访问数据库服务器时所需的用户名。 |

#### 3\. 使用 RowSet 对象与数据库建立连接

使用 RowSet 对象和数据库建立连接，JSP 代码如下：

```
<%
Class.forName(sun.jdbc.odbc.JDBCodbcDriver);    //载人 JDBC 驱动程序
rset.setURL(JDBCodbc test);
rset.setUser(sa);
rset.setPassword();    //指定 JDBC 数据源 URL
rest.setType(ResultSet.TYPE_SCROLL_SENSITIVE);
%>
```

在上面的代码段中，首先需要载入 JDBC 驱动程序，然后分别使用 setURL()、setUser()，setPassword() 等方法指定 JDBC 数据源 URL、数据库用户名、数据库访问密码等访问参数，代码段的最后使用 setType() 方法指定 RowSet 实例对象，所包含的结果记录集的类型是：数据库游标可以前后移动，并且结果记录集的数据对其他用户的数据库更新操作敏感。

其实上面的 JSP 代码段并没有真正与数据库系统建立连接，它仅仅指定了重要的数据库连接参数，使得 RowSet 处于就绪状态，随时都可以和数据库建立连接。若需要与数据库建立连接，除了指定数据库连接参数以外，第二步就必须向数据库发送 SQL 命令，设置 SQL 命令的 IN 参数，最后调用 execute() 方法，该方法的内部流程是：建立真正的数据库连接，调用数据库引擎，完成 SQL 命令，并且用数据库系统返回的结果数据填充 RowSet 对象的记录集。

这时，RowSet 对象和 ResultSet 对象差不多，也可以遍历记录集中的每一个记录，或者定位到某一个记录使用 getXXX()、updateXXX() 等方法对记录集中的数据进行操作。

#### 4\. 使用 RowSet 对象向数据库服务器发送命令

设定 RowSet 对象与数据库连接的属性以后，就可以利用 RowSet 对象向数据库发送命令和命令参数。

使用 RowSet 接口的 setCommand() 方法可以向数据库系统发送预编译的 SQL 命令，使用其他的 setXXX() 方法可以设定 SQL 命令的 IN 参数，类似于 PreparedStatement 接口和 CallableStatement 接口发送预编译 SQL 命令的方式。RowSet 接口中定义的相关方法如下。

1.  public void setCommand(java.lang.String cmd)：使用该方法可以向数据库发送预编译的 SQL 语句，SQL 语句中可以含有输入参数，参数的位置用?号代替。第一个输入参数的索引为 1，第二个参数的索引为 2，以此类推。
2.  public void setARRAY(int i java.sql.ARJRAY x)：该方法可以设定 SQL 语句中 SQL 数 据类型是 SQL ARRAY 的参数，方法参数 i 指定输入参数的索引，方法参数 x 代表替换目标参数的 java.sql.ARRAY 对象。
3.  public void setBlob(int i java.sql.Blob x)：该方法和 setARRAY() 差不多，主要用于设定 SQL 语句中数据类型为 SQL BLOB 类型的输入参数。
4.  public void setString(int parameterlndexjava.lang.String x)：该方法主要用于设定 SQL 语句中数据类型为 VARCHAR、NVARCHAR 等类型的输入参数，parameterIndex 代表需要替换的输入参数的索引，x 代表需要引入的 java String 对象。

【例 1】下面的 JSP 代码演示如何使用 RowSet 对象的 setCommand() 方法向数据库发送 SQL 命令，并且使用 setXXX() 方法指定 SQL 命令的输入参数（rset 是 RowSet 接口的实例对象）。

```
<%
rset.setCommand(SELECT * FROM tbuser where name=? and pass=?);
rset.setString(1 fancy);
rset.setString(2 fancy);
%>
```

其实，setCommand() 方法和前面讲述的 setPassword()、setURL() 等方法都没有和数据库系统建立事实上的数据库连接，它们都用于设定 RowSet 对象的属性。SQL 命令也可以看作 RowSet 对象的属性之一。

#### 5\. 使用 RowSet 对象执行数据库操作

如果己经按照上面介绍的步骤设置好 RowSet 对象的连接属性，在 SQL 命令中属性输入参数，那么现在就可以使用 RowSet 接口的 execute() 方法执行数据库操作，execute() 方法首先应用数据库连接属性与数据库建立连接，然后向数据库系统发送 SQL 命令，接受数据库返回的数据，使用这些数据实例化 RowSet 对象内部的结果记录集。

execute() 方法并不返回任何 ResultSet 接口的实例对象，它仅用数据库返回的数据填充 RowSet 对象，因为 RowSet 接口继承自 ResultSet 接口，因此具有 ResultSet 接口的全部功能，在执行了 execute() 方法以后，可以将 RowSet 对象当作一个 ResultSet 对象来使用。

【例 2】下面的 JSP 代码演示了如何使用 RowSet 对象执行数据库操作。

```
<%
rset.execute();
while(rset.next())
{
out.println(username +rset.getString(name)+<br>);
out.println(password +rset.getString(pass)+<br>);
}
rset.close();
%>
```

#### 6\. 给 RowSet 对象添加事件监听者

RowSet 对象可以和某个 RowSet 事件监听者（RowSetListener）绑定在一起，RowSet 事件监听者可以对 RowSet 对象中发生的事件作出响应，例如数据库游标的移动事件。

addRowSetListener() 方法可以使一个 RowSet 事件监听者和 RowSet 对象绑定在一起。removeRowSetListener() 方法则可以解除这种绑定关系，RowSet 事件监听者一旦和 RowSet 对象解除了绑定关系，那么它对 RowSet 对象内部所发生的事件就不能做出响应了。

这两个方法的定义如下：

public void addRowSetListener(RowSetListener listener);
public void removeRowSetListener(RowSetListener listener);