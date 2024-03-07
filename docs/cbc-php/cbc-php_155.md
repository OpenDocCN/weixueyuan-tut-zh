# PHP setAttribute() 和 getAttribute()：设置与获取 PDO 属性

> 原文：[`c.biancheng.net/view/7769.html`](http://c.biancheng.net/view/7769.html)

在 PDO 对象中有很多属性可以用来调整 PDO 的行为或获取底层驱动程序状态，详细的说明大家可以通过查看 PHP 帮助文档（[`www.php.net/pdo`](http://www.php.net/pdo)）来获取详细的 PDO 属性列表信息。如果在创建 PDO 对象时，没有在构造方法中最后一个参数设置过的属性选项，可以在对象创建完成以后，通过 PDO 对象中的 setAttribute() 和 getAttribute() 方法设置和获取这些属性的值。

## 1、getAttribute()

getAttribute() 方法只需要提供一个参数，传递一个特定的属性名称，执行成功后会返回该属性所指定的值，否则返回 NULL，语法格式如下所示：

PDO::getAttribute(int $attribute)

其中参数 $attribute 为 PDO::ATTR_* 常量中的一个，下列为应用到数据库连接中的常量：

*   PDO::ATTR_AUTOCOMMIT
*   PDO::ATTR_CASE
*   PDO::ATTR_CLIENT_VERSION
*   PDO::ATTR_CONNECTION_STATUS
*   PDO::ATTR_DRIVER_NAME
*   PDO::ATTR_ERRMODE
*   PDO::ATTR_ORACLE_NULLS
*   PDO::ATTR_PERSISTENT
*   PDO::ATTR_PREFETCH
*   PDO::ATTR_SERVER_INFO
*   PDO::ATTR_SERVER_VERSION
*   PDO::ATTR_TIMEOUT

【示例】使用 getAttribute() 方法获取属性的值。

```

<?php
    header("Content-Type: text/html;charset=utf-8");
    $opt = [PDO::ATTR_PERSISTENT=>TRUE];
    try{
        $pdo = new PDO('mysql:dbname=test;host=127.0.0.1','root','root',$opt);
    }catch(PDOException $e){
        echo '数据库连接失败：'.$e->getMessage();
        exit();
    }
    echo 'PDO 是否关闭自动提交功能：'.$pdo -> getAttribute(PDO::ATTR_AUTOCOMMIT);
    echo '<br>当前 PDO 的错误处理模式：'.$pdo -> getAttribute(PDO::ATTR_ERRMODE);
    echo '<br>表字段字符的大小写转换：'.$pdo -> getAttribute(PDO::ATTR_CASE);
    echo '<br>与连接状态相关的特有信息：'.$pdo -> getAttribute(PDO::ATTR_CONNECTION_STATUS);
    echo '<br>空字符串转换为 SQL 的 null：'.$pdo -> getAttribute(PDO::ATTR_ORACLE_NULLS);
    echo '<br>应用程序提前获取数据大小：'.$pdo -> getAttribute(PDO::ATTR_PERSISTENT);
    echo '<br>数据库特有的服务器信息：'.$pdo -> getAttribute(PDO::ATTR_SERVER_INFO);
    echo '<br>数据库服务器版本号：'.$pdo -> getAttribute(PDO::ATTR_SERVER_VERSION);
    echo '<br>数据库客户端版本号：'.$pdo -> getAttribute(PDO::ATTR_CLIENT_VERSION);
?>
```

运行结果如下：

PDO 是否关闭自动提交功能：1
当前 PDO 的错误处理模式：0
表字段字符的大小写转换：0
与连接状态相关的特有信息：127.0.0.1 via TCP/IP
空字符串转换为 SQL 的 null：0
应用程序提前获取数据大小：1
数据库特有的服务器信息：Uptime: 15474 Threads: 1 Questions: 11454 Slow queries: 0 Opens: 585 Flush tables: 1 Open tables: 256 Queries per second avg: 0.740
数据库服务器版本号：5.7.26
数据库客户端版本号：mysqlnd 5.0.10 - 20111026 - $Id: c85105d7c6f7d70d609bb4c000257868a40840ab $

## 2、setAttribute()

setAttribute() 方法可以用来设置数据库句柄的属性，语法格式如下：

PDO::setAttribute(int $attribute, mixed $value)

这个方法需要两个参数，第一个参数 $attribute 提供 PDO 对象特定的属性名，第二个参数 $value 则是为这个指定的属性赋一个值。下面列出了一些可用的通用属性名称和可以使用的值：

*   PDO::ATTR_CASE：强制列名为指定的大小写；
    *   PDO::CASE_LOWER：强制列名小写；
    *   PDO::CASE_NATURAL：保留数据库驱动返回的列名；
    *   PDO::CASE_UPPER：强制列名大写。
*   PDO::ATTR_ERRMODE：错误报告；
    *   PDO::ERRMODE_SILENT：仅设置错误代码；
    *   PDO::ERRMODE_WARNING：引发 E_WARNING 错误；
    *   PDO::ERRMODE_EXCEPTION：抛出 exceptions 异常。
*   PDO::ATTR_ORACLE_NULLS：（在所有驱动中都可用，不仅限于 Oracle）转换 NULL 和空字符串；
    *   PDO::NULL_NATURAL：不转换；
    *   PDO::NULL_EMPTY_STRING：将空字符串转换成 NULL；
    *   PDO::NULL_TO_STRING：将 NULL 转换成空字符串。
*   PDO::ATTR_STRINGIFY_FETCHES：提取的时候将数值转换为字符串；
*   PDO::ATTR_STATEMENT_CLASS：设置从 PDOStatement 派生的用户提供的语句类。不能用于持久的 PDO 实例。需要 array(string 类名, array(mixed 构造函数的参数))；
*   PDO::ATTR_TIMEOUT：指定超时的秒数。不同驱动之间可能会有差异，比如 SQLite 等待的时间达到此值后就会放弃获取可写锁，但其他驱动可能会将此值解释为一个连接或读取超时的间隔；
*   PDO::ATTR_AUTOCOMMIT：（在 OCI，Firebird 以及 MySQL 中可用）是否自动提交每个单独的语句；
*   PDO::ATTR_EMULATE_PREPARES：启用或禁用预处理语句的模拟。有些驱动不支持或有限度地支持本地预处理，使用此设置可以强制 PDO 总是模拟预处理语句，或试着使用本地预处理语句。如果驱动不能成功预处理当前查询，它将总是回到模拟预处理语句上；
*   PDO::MYSQL_ATTR_USE_BUFFERED_QUERY：（在 MySQL 中可用）使用缓冲查询；
*   PDO::ATTR_DEFAULT_FETCH_MODE：设置默认的提取模式。

【示例】使用 setAttribute() 方法设置数据库句柄的属性

```

<?php
    header("Content-Type: text/html;charset=utf-8");
    $pdo = new PDO('mysql:dbname=test;host=127.0.0.1','root','root',$opt);
    $pdo->setAttribute(PDO::ATTR_CASE, PDO::CASE_UPPER);
    echo 'PDO::ATTR_CASE 属性的值为：'.$pdo -> getAttribute(PDO::ATTR_CASE);
    $pdo->setAttribute(PDO::ATTR_CASE, PDO::CASE_LOWER);
    echo '<br>PDO::ATTR_CASE 属性的值为：'.$pdo -> getAttribute(PDO::ATTR_CASE);
    $pdo->setAttribute(PDO::ATTR_CASE, PDO::CASE_NATURAL);
    echo '<br>PDO::ATTR_CASE 属性的值为：'.$pdo -> getAttribute(PDO::ATTR_CASE);
?>
```

运行结果如下：

PDO::ATTR_CASE 属性的值为：1
PDO::ATTR_CASE 属性的值为：2
PDO::ATTR_CASE 属性的值为：0