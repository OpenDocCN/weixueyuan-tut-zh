# PHP mysqli_num_rows()：获取查询结果的行数

> 原文：[`c.biancheng.net/view/7696.html`](http://c.biancheng.net/view/7696.html)

在 PHP 中，想要获取由 SELECT 语句查询到的结果集中有多少条数据的话，则需要使用 mysqli_num_rows() 函数来实现。首先我们来看一下该函数的语法结构：

#### 面向对象风格的写法

$mysqli_result -> num_rows;

其中 $mysqli_result 为使用 mysqli_query() 函数返回的结果集。

#### 面向过程风格的写法

mysqli_num_rows(mysqli_result $result)

其中 $result 为使用 mysqli_query() 函数返回的结果集。

注意：mysqli_num_rows() 函数仅对 SELECT 语句有效，如果返回的行数大于 PHP_INI_MAX，则将行数以字符串的形式返回。

【示例】使用 mysqli_num_rows() 函数获取查询结果的行数。

#### 面向对象风格的写法

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
        $sql    = 'select name,sex,age from user';     // SQL 语句
        $result = $mysql -> query($sql);               // 执行上面的 SQL 语句
        $num    = $result -> num_rows;                 // 获取查询结果的行数
        $mysql -> close();
    }
    echo '一共查询到 '.$num.' 条记录。';
?>
```

#### 面向过程风格的写法

```

<?php
    $host     = 'localhost';
    $username = 'root';
    $password = 'root';
    $dbname   = 'test';
    $link     = @mysqli_connect($host, $username, $password, $dbname);
    if($link){
        $sql    = 'select name,sex,age from user';  // SQL 语句
        $result = mysqli_query($link, $sql);        // 执行 SQL 语句，并返回结果
        $num    = mysqli_num_rows($result);         // 获取查询结果的行数
        mysqli_close($link);
    }else{
        echo '数据库连接失败！';
    }
    echo '一共查询到 '.$num.' 条记录。';
?>
```

运行结果如下：

一共查询到 5 条记录。