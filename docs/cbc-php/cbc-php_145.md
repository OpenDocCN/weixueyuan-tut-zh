# PHP mysqli_select_db()：选择数据库

> 原文：[`c.biancheng.net/view/7690.html`](http://c.biancheng.net/view/7690.html)

通过前面《mysqli_connect() 函数》的介绍我们知道，对应数据库名称的参数是可以省略的，如果省略该参数的话，就需要我们在后面来指定一个默认的数据库。PHP 中可以使用 mysqli_select_db() 函数来指定一个默认数据库，它和 MySQL 命令中的 `use dbname` 的功能相似，函数的语法格式如下：

#### 面向对象风格的写法：

mysqli::select_db(string $dbname)

其中，$dbname 为指定的数据库名称

#### 面向过程风格的写法：

mysqli_select_db(mysqli $link, string $dbname)

其中，$link 为通过 mysqli_connect() 函数返回的数据库连接，$dbname 为指定的数据库名称。

函数执行成功会返回 TRUE，执行失败则返回 FALSE。

【示例】使用 mysqli_select_db() 函数选择默认数据库

#### 1) 面向对象风格的写法：

```

<?php
    $host     = 'localhost';
    $username = 'root';
    $password = 'root';
    $dbname   = 'test';
    $mysql    = new Mysqli($host, $username, $password);
    if($mysql -> connect_errno){
        die('数据库连接失败：'.$mysql->connect_errno);
    }else{
        $mysql -> select_db($dbname);                  // 选择名为 test 的数据库
        $sql    = 'select name,sex,age from user';     // SQL 语句
        $result = $mysql -> query($sql);               // 执行上面的 SQL 语句
        $data   = $result -> fetch_all();
        $mysql -> close();
    }
    echo '<pre>';
    print_r($data);
?>
```

#### 2) 面向过程风格的写法：

```

<?php
    $host     = 'localhost';
    $username = 'root';
    $password = 'root';
    $dbname   = 'test';
    $link     = @mysqli_connect($host, $username, $password);
    if($link){
        mysqli_select_db($link, $dbname);           // 选择名为 test 的数据库
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

通常我们可以将数据库连接和选择默认数据库的过程写在一个单独的 PHP 脚本文件中。在其他需要对数据库操作的 PHP 脚本中，只要使用 require() 或 include() 等函数将该文件包含进来即可，不需要再重复编写连接数据库的代码了。

这样做不仅可以提高开发效率，更重要的是当数据库的用户名和密码需要变化时，只需要更改这一个文件即可，所有使用该文件的 PHP 脚本都是使用新用户与数据库服务器建立的连接。