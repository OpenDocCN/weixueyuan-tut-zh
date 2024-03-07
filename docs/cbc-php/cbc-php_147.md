# PHP 获取 SQL 的查询结果

> 原文：[`c.biancheng.net/view/7692.html`](http://c.biancheng.net/view/7692.html)

前面我们介绍了如何执行一条 SQL 语句，也就是调用《mysqli_query() 函数》。当我们执行一条 SELECT 命令的 SQL 语句时，mysqli_query() 函数会返回一个 PHP 资源的引用指针（结果集），也就是这条 SQL 语句的查询结果。通常我们需要对这一结果集进行处理才能得到我们想要的信息。

在 PHP 中处理数据结果集的函数主要有以下几个：

*   mysqli_fetch_row()：从结果集中取得一行，并以索引数组的形式返回；
*   mysqli_fetch_assoc()：从结果集中取得一行，并以关联数组的形式返回；
*   mysqli_fetch_array()：从结果集中取得一行，并以关联数组、索引数组或二者兼有的形式返回；
*   mysqli_fetch_all()：从结果集中取得所有行，并以关联数组、索引数组或二者兼有的形式返回；
*   mysqli_fetch_object()：从结果集中取得一行，并以对象的形式返回。

## 1、mysqli_fetch_row() 函数

mysqli_fetch_row() 函数可以从结果集中取得一行，并以索引数组的形式返回，其语法格式如下：

#### 面向对象风格的写法

mysqli_result::fetch_row()

其中 mysqli_result 为使用 mysqli_query() 函数获取的结果集。

#### 面向过程风格的写法

mysqli_fetch_row(mysqli_result $result)

其中 $result 为使用 mysqli_query() 函数获取的结果集。

【示例】使用 mysqli_fetch_row() 函数获取结果集中的一条数据。

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
        $data   = $result -> fetch_row();
        $mysql -> close();
    }
    echo '<pre>';
    print_r($data);
?>
```

运行结果如下：

Array
(
    [0] => 张三
    [1] => 男
    [2] => 12
)

## 2、mysqli_fetch_assoc() 函数

mysqli_fetch_assoc() 函数可以从结果集中取得一行，并以关联数组的形式返回，其语法格式如下：

#### 面向对象风格的写法

mysqli_result::fetch_assoc()

其中 mysqli_result 为使用 mysqli_query() 函数获取的结果集。

#### 面向过程风格的写法

mysqli_fetch_assoc(mysqli_result $result)

其中 $result 为使用 mysqli_query() 函数获取的结果集。

【示例】使用 mysqli_fetch_assoc() 函数获取结果集中的一条数据。

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
        $data   = mysqli_fetch_assoc($result);      // 从结果集中获取一条数据
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
    [name] => 张三
    [sex]  => 男
    [age]  => 12
)

## 3、mysqli_fetch_array() 函数

mysqli_fetch_array() 函数可以从结果集中取得一行，并根据参数以关联数组、索引数组或二者兼有的形式返回，函数的语法格式如下：

#### 面向对象风格的写法

mysqli_result::fetch_array([int $resulttype = MYSQLI_BOTH])

其中 mysqli_result 为使用 mysqli_query() 函数获取的结果集；$resulttype 为可选参数，它是一个常量，用来设定返回值的类型，它的取值可以是 MYSQLI_ASSOC、MYSQLI_NUM 或 MYSQLI_BOTH。

#### 面向过程风格的写法

mysqli_fetch_array(mysqli_result $result[, int $resulttype = MYSQLI_BOTH])

其中 $result 为使用 mysqli_query() 函数获取的结果集；$resulttype 为可选参数，它是一个常量，用来设定返回值的类型，它的取值可以是 MYSQLI_ASSOC、MYSQLI_NUM 或 MYSQLI_BOTH。

【示例】使用 mysqli_fetch_array() 函数获取结果集中的数据。

```

<?php
    $host     = 'localhost';
    $username = 'root';
    $password = 'root';
    $dbname   = 'test';
    $link     = @mysqli_connect($host, $username, $password, $dbname);
    if($link){
        $sql    = 'select name,sex,age from user';          // SQL 语句
        $result = mysqli_query($link, $sql);                // 执行 SQL 语句，并返回结果
        $data   = mysqli_fetch_array($result, MYSQLI_ASSOC);// 从结果集中获取所有数据
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
    [name] => 张三
    [sex] => 男
    [age] => 12
)

## 4、mysqli_fetch_all() 函数

mysqli_fetch_all() 函数可以获取结果集中的所有数据，并根据参数以关联数组、索引数组或二者兼有的形式返回，函数的语法格式如下：

#### 面向对象风格的写法

mysqli_result::fetch_all([int $resulttype = MYSQLI_NUM])

其中 mysqli_result 为使用 mysqli_query() 函数获取的结果集；$resulttype 为可选参数，它是一个常量，用来设定返回值的类型，它的取值可以是 MYSQLI_ASSOC、MYSQLI_NUM 或 MYSQLI_BOTH。

#### 面向过程风格的写法

mysqli_fetch_all(mysqli_result $result [, int $resulttype = MYSQLI_NUM])

其中 $result 为使用 mysqli_query() 函数获取的结果集；$resulttype 为可选参数，它是一个常量，用来设定返回值的类型，它的取值可以是 MYSQLI_ASSOC、MYSQLI_NUM 或 MYSQLI_BOTH。

【示例】使用 mysqli_fetch_all() 函数获取结果集中的所有数据。

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
        $data   = $result -> fetch_all(MYSQLI_ASSOC);
        $mysql -> close();
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
            [name] => 张三
            [sex] => 男
            [age] => 12
        )
    [1] => Array
        (
            [name] => 李四
            [sex] => 男
            [age] => 11
        )
    [2] => Array
        (
            [name] => 王五
            [sex] => 男
            [age] => 13
        )
    [3] => Array
        (
            [name] => 小明
            [sex] => 女
            [age] => 12
        )
)

## 5、mysqli_fetch_object() 函数

mysqli_fetch_object() 函数可以从结果集中取得一行，并以对象的形式返回。函数的语法格式如下：

#### 面向对象风格的写法

mysqli_result::fetch_object([string $class_name = "stdClass"[, array $params]])

其中 mysqli_result 为使用 mysqli_query() 函数获取的结果集；$class_name 为可选参数，用来规定实例化的类名称，设置属性并返回；$params 为可选参数，用来规定一个传给 $classname 的构造函数的可选参数数组。

#### 面向过程风格的写法

mysqli_fetch_object(mysqli_result $result[, string $class_name = "stdClass"[, array $params]])

其中 $result 为使用 mysqli_query() 函数获取的结果集；$class_name 为可选参数，用来规定实例化的类名称，设置属性并返回；$params 为可选参数，用来规定一个传给 $classname 的构造函数的可选参数数组。

【示例】使用 mysqli_fetch_object() 函数返回结果集中的当前行，并输出每个字段的值。

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
        $sql = 'select name,sex,age from user';     // SQL 语句
        $result = $mysql -> query($sql);            // 执行上面的 SQL 语句
        if($result){
            while($obj = $result -> fetch_object()){
                printf('姓名：%s，性别：%s，年龄：%s <br>', $obj->name,$obj->sex,$obj->age);
            }
        }
        $mysql -> close();
    }
?>
```

运行结果如下：

姓名：张三，性别：男，年龄：12
姓名：李四，性别：男，年龄：11
姓名：王五，性别：男，年龄：13
姓名：小明，性别：女，年龄：12