# JDBC BaseRowSet 类和 CachedRowSet 类

CachedRowSet 包就是 sun.jdbc.RowSet 包，用户可以在网站上载 CachedRowSet 包。在 CachedRowSet 包中有一个抽象类 BaseRowSet，它是 CachedRowSet 类和 JdbcRowSet 类的直接父类，也是 WebRowSet 类的间接父类。

## BaseRowSet 类

BaseRowSet 类继承自 java.lang.Object，它是一个抽象类（abstract），在 BaseRowSet 类的定义中，它仅仅声明了成员方法名而没有实现成员方法的功能，这些功能需要在继承 BaseRowSet 类的类或者接口中实现。CachedRowSet 类和 JdbcRowSet 类都实现了 BaseRowSet 类中声明的成员方法，所以它们都不是抽象类。

在 BaseRowSet 类中主要定义的成员方法和成员变量如下。

#### 1\. 设置或者获取 XXXRowSet 对象与数据库建立连接的连接参数

首先需要声明，这里所说的 XXXRowSet 对象，指的是 CachedRowSet 类、JdbcRowSet 类、WebRowSet 类的实例对象，而不是指 javax.sql.RowSet 接口的实例对象，对于这一点，读者一定要特别注意。

BaseRowSet 声明了 setPassword()、getPassword()、setUsemme()、getUsemame()、setURL()、getURL() 等方法，这些方法主要用于指定或者获取 RowSet 对象与数据库建立连接的连接参数，类似的方法还有很多，如 setType()、setTypeMap()、getType()、getTypeMap() 等。

读者应该不难发现，这些方法和 javax.sql、RowSet 接口中定义的方法一模一样，不过这两者之间似乎没有任何联系，BaseRowSet 类继承自 java.lang.Object，而 RowSet 接口继承自 java.sql.ResultSet 接口。

#### 2\. XXXRowSet 对象的监听器

BaseRowSet 类中声明了 addRowSetListener() 方法和 removeRowSetListener() 方法，这两个方法分别用于将一个事件监听器绑定在 XXXRowSet 对象上和解除这种绑定关系。javax.sql.RowSet 接口也定义了类似的方法。

#### 3\. 发送 SQL 命令和设定 SQL 命令的输入参数（IN 参数）

BaseRowSet 类同样声明了 setCommand()、execute() 方法，分别用于向数据库系统发送 SQL 命令和执行 SQL 命令，并用数据库返回的数据填充 XXXRowSet 对象。

BaseRowSet 类也声明了一整套的 setXXX() 方法，用于指定 SQL 命令的输入参数（IN 参数），如 setString()、setBlob()、setClob()、setObject() 等方法。javax.sql.RowSet 接口也定义了类似的方法。

#### 4\. 流成员变量

BaseRowSet 类还定义了若干个流成员变量，用于保存 getXXXStream() 方法返回的流类对象，这些成员变量如下。

*   protected java.io.InputStream asciiStream ：成员变量 asciiStream 用于保存 getAsciiStream() 方法返回的 ASCII 流，这是 javaio.InputStream 类型的变量。
*   protected java.io.Reader charStream ：成员变量 charStream 用于保存 getCharacterStream() 方法返回的 char 流，这是 java.io.Reader 类型的变量。
*   protected java.io.InputStream unicodeStream：成员变量 unicodeStream 用于保存 getUnicodeStream() 方法返回的 Unicode 流，这是 java io.InputStream 类型的变量。
*   protected java.io.InputStream binary Stream：成员变量 binaryStream 主要用于保存 getBinaryStream() 方法返回的 binary 流，这是 java.io.InputStream 类型的变量。

## CachedRowSet 类

CachedRowSet 类是 CachedRowSet 包中最重要的类，它继承自 BaseRowSet 类。CachedRowSet 对象为规范的数据提供了一个无连接的（disconnected）、可串行化的（serializable）、可以滚动的（scrollable，指数据指针可以前后移动）容器。

CachedRowSet 对象可以简单地看作是一个与数据库断开连接的结果记录集，它被缓存在数据源之外，因为所有的数据都被缓存在内存之中，所以 CachedRowSet 对象不适合于处理含有海量数据的记录集。

CachedRowSet 对象的重要作用是：它可以作为数据容器，在不同的应用程序的不同组件之间传送数据。例如，一个运行于 Application Server 上的 Enterprise JavaBean 组件可以使用 JDBCAPI 访问数据库，然后可以使用 CachedRowSet 对象将数据库返回的数据通过网络发送到运行于客户端浏览器上的 Java Applet 程序或者 JavaBean 组件。

如果客户端由于资源的限制或者出于安全上的考虑，没有办法使用 JDBC 数据库驱动程序，例如 Personal Java Clients、Personal Digital Assistant(PDA)、Network Computer(NC) 等客户端，这时使用 CachedRowSet 类就可以提供一个 Java Client 用以处理数据库的规范数据。

CachedRowSet 类的第三个作用是：它可以通过使用数据源以外的空间缓存记录集的数据，从而不需要 JDBC 数据库驱动程序帮助，就实现了结果记录集的数据库游标的前后移动。

CachedRowSet 对象获取数据以后，就断开了和数据源的连接，只有执行更新操作时，才再度与数据库建立连接。某些 jDBC 驱动程序目前仍然不支持结果记录集的数据库游标的前后移动，这时可以使用 CachedRowSet 类来实现。

#### 提示：

如果使用的 JDBC 数据库驱动程序是 JDBC-ODBC 桥驱动程序，则用 ResultSet 接口、Statement 接口以通常的方法访问数据库，似乎无法实现记表集的数据库游标的前后移动，特别是不能向后移动亦不能定位到任意行，如果读者碰到了类似的问题，除了更换 JDBC 驱动程序以外不妨使用 CachedRowSet 类。

下面我们详细介绍如何使用 CachedRowSet 类。

#### 1\. 创建 CachedRowSet 类的实例对象

如果想使用 CachedRowSet 类的强大功能，必须首先创建 CachedRowSet 类的实例对象，如何创建呢?可以使用 CachedRowSet 类的构造函数，CachedRowSet 类的构造函数是 CachedRowSet()，该函数没有任何参数。CachedRowSet() 函数初始化了下面的属性值：

onInsertRow=false
insertRow=null
cursorPos=0
numRows=0
showDeleted=false
queryTimeout=0
maxRows=0
maxFieldSize=0
RowSetType=ResultSet.TYPE_SCROLL_INSENSITIVE
concurrency=ResultSet.CONCUR_READ_ONLY
readonly=false
isolation=Connection.TRANSACTION_READ_COMMITTED
escapeProcessing=true
absolutePos=0

新创建的 CachedRowSet 对象默认可以容纳 100 个记录所包含的数据。

指定 CachedRowSet 对象和数据库建立连接的连接属性，就可以使用 setPassword()、setUsemame()、setURL() 等方法指定 CachedRowSet 对象和数据库建立连接的连接参数（当然了，需要首先载入 JDBC 驱动程序）。

setPassword()、setUsemame() 等方法都是在 BaseRowSet 类中声明的，然后在 CachedRowSet 类中实现方法的功能。

#### 2\. 使用记录集数据填充 CachedRowSet 对象

使用记录集的数据填充 CachedRowSet 对象的方法如下。

① 和 javax.sql.RowSet 接口所使用的方法如出一辙，设定了 CachedRowSet 对象的数据库连接参数以后调用 setCommand() 方法指定 SQL 命令，再使用 setXXX() 方法设定 SQL 命令的输入参数（如果有输入参数的话），接着就可以使用 execute() 方法首先利用设定好的连接参数和数据库建立连接，发送 SQL 命令，执行 SQL 命令，获取数据库返回的数据，并用这些数据填充 CachedRowSet 对象的内部记录集结构。注意这种方法所使用的 execute() 方法不带任何参数。

② 首先载入 JDBC 数据库驱动程序，然后与数据库建立连接，创建 Connection 接口的实例对象，接着用 setCommand() 方法指定 SQL 命令，如果存在 SQL 输入参数则可以使用 setXXX() 方法指定 IN 参数，一切就绪后，就可以调用 execute() 方法。execute() 方法可以往数据库发送 SQL 命令，并用数据库服务器返回的数据填充 CachedRowSet 对象。两个 execute() 方法的定义如下：

public void execute(java.sql.Connection connection);
public void execute();

③ 首先载入 JDBC 数据库驱动程序，分别创建 Connection 接口的实例对象，Statement 接口的实例对象接着调用 Statement 对象的 execute() 方法执行数据库操作，返回一个 ResultSet 接口的实例对象，然后就可以使用 CachedRowSet 类的 populate() 方法将 ResultSet 对象的数据填充到 CachedRowSet 对象内部的记录集结构中。

populate() 方法的定义如下：

public void populate(java.sql.ResultSet data)