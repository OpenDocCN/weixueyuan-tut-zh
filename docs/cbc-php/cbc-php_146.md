# PHP mysqli_query()：执行 SQL 语句

> 原文：[`c.biancheng.net/view/7673.html`](http://c.biancheng.net/view/7673.html)

成功选择好 MySQL 数据库后，接下来就可以对所选数据库中的数据表进行查询、更改以及删除等操作，PHP 中我们使用 mysqli_query() 函数就可以实现上述的所有操作，函数的语法格式如下：

#### 面向对象风格的写法：

mysqli::query( string $query [, int $resultmode = MYSQLI_STORE_RESULT ] )

参数说明：

*   $query：要执行的 SQL 语句；
*   $resultmode：可选参数，用来修改函数的行为。可以是下列值的任意一个：
    *   MYSQLI_USE_RESULT（如果需要查询大量数据，使用这个）；
    *   MYSQLI_STORE_RESULT（默认值）。

#### 面向过程风格的写法：

mysqli_query( mysqli $link , string $query [, int $resultmode = MYSQLI_STORE_RESULT ] )

参数说明：

*   $link：表示由 mysqli_connect() 函数返回的数据库连接；
*   $query：要执行的 SQL 语句；
*   $resultmode：可选参数，用来修改函数的行为。可以是下列值的任意一个：
    *   MYSQLI_USE_RESULT（如果需要查询大量数据，使用这个）；
    *   MYSQLI_STORE_RESULT（默认值）。

提示：函数执行失败时会返回 FALSE；而通过 mysqli_query() 成功执行 SELECT、SHOW、DESCRIBE 或 EXPLAIN 查询时则会返回一个 mysqli_result 对象；其他查询执行成功则返回 TRUE。

演示之前我们先在数据表中添加一些数据，如下所示：

mysql> select * from user;
+----+--------+------+------+
| id | name   | age  | sex  |
+----+--------+------+------+
|  1 | 张三   |   12 | 男   |
|  2 | 李四   |   11 | 男   |
|  3 | 王五   |   13 | 男   |
|  4 | 小明   |   12 | 女   |
+----+--------+------+------+
4 rows in set (0.00 sec)

【示例】使用 mysqli_query() 函数执行指定的 SQL 语句。

#### 1) 面向对象风格的写法

```

<?php
    $host     = 'localhost';
    $username = 'root';
    $password = 'root';
    $dbname   = 'test';
    $mysql    = new Mysqli($host, $username, $password, $dbname);
    if($mysql -> connect_errno){
        die('数据库连接失败：'.$mysql->connect_errno);
    }else{
        $mysql -> set_charset('UTF-8');             // 设置数据库字符集
        $sql = 'select name,sex,age from user';     // SQL 语句
        $result = $mysql -> query($sql);            // 执行上面的 SQL 语句
        $data = $result -> fetch_all();
        $mysql -> close();
    }
    echo '<pre>';
    print_r($data);
?>
```

#### 2) 面向过程风格的写法

```

<?php
    $host     = 'localhost';
    $username = 'root';
    $password = 'root';
    $dbname   = 'test';
    $link = @mysqli_connect($host, $username, $password, $dbname);
    if($link){
        $sql    = 'select name,sex,age from user';  // SQL 语句
        $result = mysqli_query($link, $sql);        // 执行 SQL 语句，并返回结果
        $data   = mysqli_fetch_all($result);        // 从结果集中获取所有数据
        mysqli_close($link);
    }else{
        echo '数据库连接失败！';
    }
    echo '<pre>';
    print_r($data);
?>
```

运行结果如下：

Array
(
    [0] => Array
        (
            [0] => 张三
            [1] => 男
            [2] => 12
        )
    [1] => Array
        (
            [0] => 李四
            [1] => 男
            [2] => 11
        )
    [2] => Array
        (
            [0] => 王五
            [1] => 男
            [2] => 13
        )
    [3] => Array
        (
            [0] => 小明
            [1] => 女
            [2] => 12
        )
)