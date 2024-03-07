# 建立 JDBC 连接

Connection 对象代表与数据库的连接。连接过程包括所执行的 SQL 语句和在该连接上返回的结果。一个应用程序可与单个数据库有一个或多个连接，或者可与许多数据库有连接。

## 打开连接

与数据库建立连接的标准方法是调用 DriverManager.getConnection 方法。该方法接受含有某个 URL 的字符串。DriverManager 类（即所谓的 JDBC 管理层）将尝试找到可与那个 URL 所代表的数据库进行连接的驱动程序。

DriverManager 类存有己注册的 Driver 类的清单。当调用 getConnection 方法时，它将检查清单中的每个驱动程序，直到找到可与 URL 中指定的数据库进行连接的驱动程序为止。Driver 的方法 connect 使用这个 URL 来建立实际的连接。

用户可绕过 JDBC 管理层直接调用 Driver 方法。这在以下特殊情况很有用：当两个驱动器可同时连接到数据库中，而用户需要明确地选用其中特定的驱动器时。但一般情况下，让 DriverManager 类处理打开连接这种事将更为简单。

下述代码显示了如何打开一个 URL="JDBC:ODBC:wombat" 的数据库连接。所用的用户名为 sa，口令为 sa：

```
String url="JDBC:ODBC:wombat";
Connection con=DriverManage.getConnection(url,"sa","sa");
```

或

```
private static String dbdriver="org.gjt.mm.mysql.Driver";
private static String connstr="jdbc:mysql://localhost/example?user=root&password=1234&useUnicode=true&characterEncoding=8859_1";
```

## 一般用法的 URL

URL 的中文含义是统一资源定位符，提供在 Internet 上定位资源所需的信息，可将它想象为一个地址。URL 的第一部分指定了访问信息所用的协议，后面总是跟着冒号。

常用的协议有 ftp（代表“文件传输协议”）和 http（代表“超文本传输协议”）。如果协议是 file，表示资源是在某个本地文件系统上而非在 Internet 上（下例用于表示我们所描述的部分；它并非 URL 的组成部分）。

ftp://JavaSoft.com/docs/JDK-1_apidocs.zip
http://Java.sun.com/products/jdk/CurrentRelease
file:/home/haroldw/docs/books/tutorial/summary.html

URL 的其余部分（冒号后面的）给出了数据资源所处位置的有关信息。如果协议是 file，则 URL 的其余部分是文件的路径。对于 ftp 和 http 协议，URL 的其余部分标识了主机并可选地给出某个更详尽的地址路径。

例如，以下是 JavaSoft 主页的 URL。该 URL 只标识了主机：http://Java.sun.com。从该主页开始浏览，就可以进入其他网页，其中之一就是 JDBC 主页。JDBC 主页的 URL 更为具体，它的具体表示如下：

http://Java.sun.com/products/JDBC

## JDBC URL

JDBC URL 提供了一种标识数据库的方法，可以使相应的驱动程序能识别该数据库并与之建立连接。

实际上，驱动程序编程员将决定用什么 JDBC URL 来标识特定的驱动程序。用户不必关心如何形成 JDBC URL，他们只需使用与所用的驱动程序一起提供的 URL 即可。JDBC 的作用是提供某些约定，驱动程序编程员在构造他们的 JDBC URL 时应该遵循这些约定。

由于 JDBC URL 要与各种不同的驱动程序一起使用，因此这些约定应非常灵活。首先，它们应允许不同的驱动程序使用不同的方案来命名数据库。例如，ODBC 子协议允许（但并不是要求）URL 含有属性值。

其次，JDBC URL 应允许驱动程序编程员将一切所需的信息编入其中。这样就可以让要与给定数据库对话的  applet 打开数据库连接，而无须要求用户去做任何系统管理工作。

最后，JDBC URL 应允许某种程度的间接性。也就是说，JDBC URL 可指向逻辑主机或数据库名，而这种逻辑主机或数据库名将由网络命名系统动态地转换为实际的名称。这可以使系统管理员不必将特定主机声明为 JDBC 名称的一部分。网络命名服务有多种（例如 DNS、NIS 和 DCE），而对于使用哪种命名服务并无限制。

JDBC URL 由三部分组成”各部分之间用冒号分隔。

JDBC URL 的三个部分可分解如下：

1.  JDBC 协议：JDBC URL 中的协议总是 JDBC。
2.  <子协议>：驱动程序名或数据库连接机制（这种机制可由一个或多个驱动程序支持）的名称。子协议名的典型示例是 ODBC，该名称是为用于指定 ODBC 风格的数据资源名称的 URL 专门保留的。例如，为了通过 JDBC-ODBC 桥来访问某个数据库，可以使用 JDBC:ODBC:BOOK。本例中，子协议为 ODBC，子名称 BOOK 是本地 ODBC 数据资源。如果要用网络命名服务（这样 JDBC URL 中的数据库名称不必是实际名称），则命名服务可以作为子 协议。例如，可使用 JDBC:dcenaming:accounts，该 URL 指定了本地 DCE 命名服务应该将数据库名称 accounts 解析为更为具体的可用于连接真实数据库的名称。
3.  <子名称>：标识数据库的方法。子名称可以依不同的子协议而变化。使用子名称的目的是为定位数据库提供足够的信息。因为 ODBC 将提供其余部分的信息，因此用 book 就已足够。然而，位于远程服务器上的数据库需要更多的信息。例如，如果数据库是通过 Internet 来访问的，则在 IDBC URL 中应将网络地址作为子名称的一部分包括进去，且必须遵循如下所示的标准 URL 命名约定：//主机名：端口/子协议。

假设 dbnet 是用于将某个主机连接到 Internet 上的协议，则 JDBC URL 应为 JDBC:dbnet://wombat:356/fred。

## ODBC 子协议

子协议 ODBC 是一种特殊情况。它是为用于指定 ODBC 风格的数据资源名称的 URL 而保留的，并具有下列特性：允许在子名称（数据资源名称）后面指定任意多个属性值。ODBC 子协议的完整语法如下：

JDBC:ODBC:<数据资源名称>;
<属性名>=<属性值>

以下都是合法的 JDBGODBC 名称：

JDBC:ODBC:qeor7
JDBC:ODBC:wombat
JDBC:ODBC:wombat;CacheSize=20;ExtensionCase=LOWER
JDBC:ODBC:qeora;UID=kgh;PWD=fooey

## 注册子协议

驱动程序编程员可保留某个名称以将其用作 JDBC URL 的子协议名。当 DriverManager 类将此名称加到已注册的驱动程序清单时，为之保留该名称的驱动程序应能识别该名称并与它所标识的数据库建立连接。例如，ODBC 是为 JDBC-ODBC 桥保留的。假设有个 Miracle 公司，它可能会将 miracle 注册为连接到其 Miracle DBMS 上的 JDBC 驱动程序的子协议，从而使其他人都无法使用这个名称。

JavaSoft 目前作为非正式代理负责注册 JDBC 子协议名称。要注册某个子协议名称，请发送电子邮件到下述地址：`JDBC@wombat.eng.sun.com`。

## 发送 SQL 语句

连接一旦建立，就可用来向它所涉及的数据库传送 SQL 语句。JDBC 对可被发送的 SQL 语句类型不加任何限制。这就提供了很大的灵活性，即允许使用特定的数据库语句甚至于非 SQL 语句。然而，它要求用户自己负责确保所涉及的数据库可以处理所发送的 SQL 语句，否则将自食其果。例如，如果某个应用程序试图向不支持储存程序的 DBMS 发送储存程序调用，就会失败并拋出异常。JDBC 要求驱动程序应至少能提供 ANSI SQL -2 Entry Level 功能才可算是符合 JDBC 标准 TM 的。这意味着用户至少可信赖这一标准级别的功能。

JDBC 提供了三个类，用于向数据库发送 SQL 语句。Connection 接口中的三个方法可用于创建这些类的实例。下面列出这些类及其创建方法。

#### 1\. Statement

Statement 由方法 createStatement 创建。Statement 对象用于发送简单的 SQL 语句。

#### 2\. PreparedStatement

PreparedStatement 由方法 prepareStatement 创建。PreparedStatement 对象用于发送带有一个或多个输入参数（IN 参数）的 SQL 语句。PreparedStatement 拥有一组方法，用于设置 IN 参数的值。执行语句时，这些 IN 参数将被送到数据库中。

PreparedStatement 的实例扩展了 Statement，因此它们都包括 Statement 的方法。PreparedStatement 对象有可能比 Statement 对象的效率更高，因为它已被预编译过并存放在那以供将来使用。

#### 3\. CallableStatement

CallableStatement 由方法 prepareCall 创建。CallableStatement 对象用于执行 SQL 储存程序——一组可通过名称来调用（就像函数的调用那样）的 SQL 语句。CallableStatement 对象从 PreparedStatement 中继承了用于处理 IN 参数的方法，而且还增加了用于处理 OUT 参数和 INPUT 参数的方法。

不过通常来说，createStatement 方法用于调用简单的 SQL 语句（不带参数）、prepareStatement 方法用于调用带一个或多个 IN 参数的 SQL 语句或经常被执行的简单的 SQL 语句，而 prepareCall 方法用于调用已储存过程。

## 事务

事务由一个或多个己被执行、完成并被提交或还原的语句组成。

当调用方法 commit 或 rollback 时，当前事务即告结束，另一个事务随即开始。缺省情况下，新连接将处于自动提交模式。也就是说，当语句执行完后，将自动对那个语句调用 commit 方法。这种情况下，由于每个语句都是被单独提交的，因此一个事务只由一个语句组成。

如果禁用自动提交模式，事务将要等到 commit 或 rollback 方法被显式调用时才结束，因此它将包括上一次调用 commit 或 rollback 方法以来所有执行过的语句。对于这种情况，事务中的所有语句将作为组来提交或还原。

方法 commit 使 SQL 语句对数据库所做的任何更改都成为永久性的，它还将释放事务持有的全部锁。而方法 rollback 将弃去那些更改。

若用户在另一个更改生效前不想让此更改生效，可通过禁用自动提交并将两个更新组合在一个事务中来实现。如果两个更新都是成功，则调用 commit 方法，从而使两个更新结果成为永久性的；如果其中之一或两个更新都失败了，则调用 rollback 方法，以将值恢复为更新之前的值。

大多数 JDBC 驱动程序都支持事务。事实上，符合 JDBC 的驱动程序必须支持事务。 DatabaseMetaData 给出的信息描述了 DBMS 所提供的事务支持水平。

## 事务隔离级别

如果 DBMS 支持事务处理，它必须有某种途径来管理两个事务同时对一个数据库进行操作时可能发生的冲突。用户可指定事务隔离级别，以指明 DBMS 应该花多大精力来解决潜在冲突。例如，当一个事务更改了某个值而第二个事务却在该更改被提交或还原前读取该值，这该怎么办。

假设第一个事务被还原后，第二个事务所读取的更改值将是无效的，那么是否可允许这种冲突？JDBC 用户可用以下代码来指示 DBMS 允许在值被提交前读取该值（dirty 读取），其中 con 是当前连接：

```
con.setTransactionIsolation(TRANSACTION_READ_UNCOMMITTED);
```

事务隔离级别越高，为避免冲突所花的精力也就越多。Connection 接口定义了五级，其中最低级别指定了根本就不支持事务，而最高级别则指定当事务在对某个数据库进行操作时，任何其他事务不得对那个事务正在读取的数据进行任何更改。

通常，隔离级别越高，应用程序执行的速度也就越慢（原因是用于锁定的资源耗费增加了，而用户间的并发操作减少了）。在决定釆用什么隔离级别时，开发人员必须在性能需求和数据一致性需求之间进行权衡。当然，实际所能支持的级别取决于所涉及的 DBMS 功能。

当创建 Connection 对象时，其事务隔离级别取决于驱动程序，但通常是所涉及的数据库的缺省值。用户可通过调用 setlsolationLevel 方法来更改事务隔离级别。新的级别将在该连接过程的剩余时间内生效。

要想只改变一个事务的事务隔离级别，必须在该事务开始前进行设置，并在该事务结束后进行复位。我们不提倡在事务的中途对事务隔离级别进行更改，因为这将立即触发 commit 方法的调用，使在此之前所作的任何更改变成永久性的。