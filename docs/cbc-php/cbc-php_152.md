# PHP 使用 PDO 执行 SQL 语句

> 原文：[`c.biancheng.net/view/7740.html`](http://c.biancheng.net/view/7740.html)

在 PDO 中，我们可以使用三种方式来执行 SQL 语句，分别是 exec() 方法，query() 方法，以及预处理语句 prepare() 和 execute() 方法。下面就来分别介绍一下。

## 1) exec() 方法

当执行 INSERT、UPDATE 和 DELETE 等不需要返回结果集的 SQL 语句时，可以使用 PDO 对象中的 exec() 方法。该方法成功执行后，将返回受影响的行数，语法格式如下：

PDO::exec(string $sql)

其中 $sql 为要执行的 SQL 语句。

注意：exec() 方法不会从 SELECT 查询语句中获取相应的结果。

【示例】使用 exec() 方法向数据库中添加一条数据。

```

<?php
    $dsn  = 'mysql:host=127.0.0.1;dbname=test';
    $user = 'root';
    $pwd  = 'root';
    try{
        $pdo = new PDO($dsn,$user,$pwd);
        $sql = "insert into user(name,age,sex) values('小刚','14','男')";
        $res = $pdo -> exec($sql);
        if($res) echo '成功添加 '.$res.' 条数据！';
    }catch(PDOException $e){
        echo '数据库连接失败：'.$e -> getMessage();
    }
?>
```

运行结果如下：

成功添加 1 条数据！

## 2) query() 方法

当执行需要返回结果集的 SELECT 查询语句时，可以使用 PDO 对象中的 query() 方法。如果该方法执行成功，则会返回一个 PDOStatement 对象。如果使用了 query() 方法，并想了解获取的数据行总数，可以使用 PDOStatement 对象中的 rowCount() 方法获取。

query() 方法的语法格式如下：

PDO::query(string $sql)
PDO::query(string $sql, int $PDO::FETCH_COLUMN, int $colno)
PDO::query(string $sql, int $PDO::FETCH_CLASS, string $classname, array $ctorargs)
PDO::query(string $sql, int $PDO::FETCH_INTO, object $object)

其中 $sql 为要执行的 SQL 语句；其余的参数用来为语句设置默认的获取模式，相当于调用结果对象 PDOStatement::setFetchMode() 方法。

【示例】使用 query() 方法查询前面我们添加的那条数据。

```

<?php
    $dsn  = 'mysql:host=127.0.0.1;dbname=test';
    $user = 'root';
    $pwd  = 'root';
    try{
        $pdo = new PDO($dsn,$user,$pwd);
        $sql = "SELECT * FROM user WHERE name='小刚'";
        $res = $pdo -> query($sql,PDO::FETCH_ASSOC);
        print_r($res);
    }catch(PDOException $e){
        echo '数据库连接失败：'.$e -> getMessage();
    }
?>
```

运行结果如下：

PDOStatement Object ( [queryString] => SELECT * FROM user WHERE name='小刚' )

使用 query() 和 exec() 方法有以下几点需要注意：

*   query() 和 exec() 都可以执行所有的 SQL 语句，只是返回值不同而已；
*   query() 可以实现所有 exec() 的功能；
*   当把 select 语句应用到 exec() 时，总是返回 0；
*   如果要看查询的具体结果，可以通过 foreach 语句完成循环输出。

## 3) prepare() 和 execute() 方法

当同一个查询需要多次执行时（有时需要迭代传入不同的参数），使用预处理语句的方式来实现效率会更高。使用预处理语句就需要使用 PDO 对象中的 prepare() 方法去准备一个将要执行的查询，再使用 PDOStatement 对象中的 execute() 方法来执行。

prepare() 方法的语法格式如下：

PDO::prepare(string $statement[, array $driver_options = array()])

参数说明如下：

*   $statement：必须是对目标数据库有效的 SQL 语句模板；
*   $driver_options：为可选参数（数组类型），包含一个或多个键值对，为返回的 PDOStatement 对象设置属性。最常使用到是将 PDO::ATTR_CURSOR 值设置为 PDO::CURSOR_SCROLL 来请求一个可滚动游标。

SQL 语句模板中可以包含零个或多个参数占位标记，格式可以是命名（:name）或问号（?）的形式，当它执行时将用真实数据取代。在同一个 SQL 语句里，命名和问号形式不能同时使用，只能选择其中一种参数形式。如果使用命名形式的占位标记，那么标记的命名必须是唯一的。

execute() 方法的语法格式如下：

PDOStatement::execute([array $input_parameters])

参数 $input_parameters 为一个元素个数和将被执行的 SQL 语句中绑定的参数一样多的数组。绑定的值不能超过指定的个数，如果在 $input_parameters 中存在比 PDO::prepare() 预处理的 SQL 指定的多的键名，则此语句将会失败并发出一个错误。

【示例】使用命名形式的参数占位符，查询指定的 SQL 语句。

```

<?php
    $dsn  = 'mysql:host=127.0.0.1;dbname=test';
    $user = 'root';
    $pwd  = 'root';
    try{
        $pdo = new PDO($dsn,$user,$pwd);
        $sql = "SELECT name,age,sex FROM user WHERE age = :age";
        $sth = $pdo -> prepare($sql);
        $sth -> execute([':age'=>11]);
        $res1 = $sth -> fetchAll();
        $sth -> execute(array(':age'=>14));
        $res2 = $sth -> fetchAll();
        echo '<pre>';
        print_r($res1);
        print_r($res2);
    }catch(PDOException $e){
        echo '数据库连接失败：'.$e -> getMessage();
    }
?>
```

运行结果如下：

Array
(
    [0] => Array
        (
            [name] => 李四
            [0] => 李四
            [age] => 11
            [1] => 11
            [sex] => 男
            [2] => 男
        )
    [1] => Array
        (
            [name] => 小红
            [0] => 小红
            [age] => 11
            [1] => 11
            [sex] => 女
            [2] => 女
        )
)
Array
(
    [0] => Array
        (
            [name] => 小刚
            [0] => 小刚
            [age] => 14
            [1] => 14
            [sex] => 男
            [2] => 男
        )
)

【示例】使用问号形式的参数占位符，查询指定的 SQL 语句。

```

<?php
    $dsn  = 'mysql:host=127.0.0.1;dbname=test';
    $user = 'root';
    $pwd  = 'root';
    try{
        $pdo = new PDO($dsn,$user,$pwd);
        $sql = "SELECT name,age,sex FROM user WHERE age = ? AND sex = ?";
        $sth = $pdo -> prepare($sql);
        $sth -> execute([12,'男']);
        $res1 = $sth -> fetchAll();
        $sth -> execute(array(11,'女'));
        $res2 = $sth -> fetchAll();
        echo '<pre>';
        print_r($res1);
        print_r($res2);
    }catch(PDOException $e){
        echo '数据库连接失败：'.$e -> getMessage();
    }
?>
```

运行结果如下：

Array
(
    [0] => Array
        (
            [name] => 张三
            [0] => 张三
            [age] => 12
            [1] => 12
            [sex] => 男
            [2] => 男
        )
    [1] => Array
        (
            [name] => 王五
            [0] => 王五
            [age] => 12
            [1] => 12
            [sex] => 男
            [2] => 男
        )
)
Array
(
    [0] => Array
        (
            [name] => 小红
            [0] => 小红
            [age] => 11
            [1] => 11
            [sex] => 女
            [2] => 女
        )
)