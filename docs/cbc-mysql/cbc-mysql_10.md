# Mysql 使用 SQL 的安全问题，Mysql 防止 SQL 注入

## SQL 注入简述

SQL Injection 攻击具有很大的危害，攻击者可以利用它读取、修改或者删除数据库内的数据，获取数据库中的用户名和密码等敏感信息，甚至可以 获得数据库管理员的权限。如果能够再利用 SQLServer 扩展存储过程和自定义扩展存储过程来执行一些系统命令，攻击者还可以获得该系统的控制权。而且，SQL Injection 也很难防范。网站管理员无法通过安装系统补丁或者进行简单的安全配置进行自我保护，一般的防火墙也无法拦截 SQL Injection 攻击。

**SQL Injection 原理：**
结构化查询语言(SQL)是一种用来和数据库交互的文本语言。SQL Injection 就是利 用某些数据库的外部接口把用户数据插入到实际的数据库操作语言(SQL)当中，从而达到入侵数据库乃至操作系统的目的。它的产生主要是由于程序对用户输入 的数据没有进行严格的过滤，导致非法数据库查询语句的执行。

如下面的用户登陆验证程序：
    $sql = "SELECT * FROM user WHERE username='$username' AND password='$password'";
    $result = mysql_db_query($dbname, $sql);
如果我们提交如下 url：
    http://127.0.0.1/injection/user.php?username=angel' or '1=1
那么就可以成功登陆系统，但是很显然这并不是我们预期的，同样我们也可以利用 sql 的注释语句实现 sql 注入，如下面的例子：
    http://127.0.0.1/injection/user.php?username=angel'/*
    http://127.0.0.1/injection/user.php?username=angel'%23
这样就把后面的语句给注释掉了！说说这两种提交的不同之处，我们提交的第一句是利用逻辑运算，第二、三句是根据 mysql 的特性，mysql 支持/*和#两种注释格式，所以我们提交的时候是把后面的代码注释掉，值得注意的是由于编码问题，在 IE 地址栏里提交#会变成空的，所以我们在地址栏提交的时候，应该提交%23，才会变成#，就成功注释了， 这个比逻辑运算简单得多了。

## 开发中可以采取的措施

**1) prepareStatement + Bind-variable**
对 Java 和 Jsp 开发的应用，可以使用  prepareStatement + Bind-variable 来防止 sql 注入，另外从 PHP 5 开始，也在扩展的 mysqli 中支持 prepared statements，所以在使用这类语言作数据库开发时，强烈建议使用 prepareStatement + Bind-variable 来实现，而尽量不要使用拼接的 sql。

**2) 使用应用程序提供的转换函数**
很多应用程序接口都提供了对特殊的字符进行转换的函数，恰当的使用这些函数，可以防止应用程序用户输入使应用程序生成不期望的效果的语句的数值。

*   MySQL C API：使用 mysql_real_escape_string() API 调用。
*   MySQL++：使用 escape 和 quote 修饰符。
*   PHP：使用 mysql_real_escape_string()函数（适用于 PHP 4.3.0，之前的版本请使用 mysql_escape_string(), PHP 4.0.3 之前的版本请使用 addslashes())。从 PHP 5 开始,可以使用扩展的 mysqli，这是对 MYSQL 新特性的一个扩展支持，其中的一个优点就是支持 prepared statements。
*   Perl DBI    ：使用 placeholders 或者 quote()方法。
*   Ruby DBI    ：使用 placeholders 或者 quote()方法。
*   Java JDBC   ：使用 PreparedStatement 和 placeholders。

**3) 自己定义函数进行校验**
如果现有的转换函数仍然不能满足要求，则需要自己编写函数进行输入校验。输入验证是一个很复杂的问题。输入验证的途径可以分为以下几种：

*   整理数据使之变得有效；
*   拒绝已知的非法输入；
*   只接受已知的合法的输入。

所以如果想要获得最好的安全状态，目前最好的解决办法就是对用户提交或者可能改变的数据进行简单分类，分别应用正则表达式来对用户提供的输入数据进行严格的检测和验证。