# PHP mysqli_multi_query()：一次执行多条 SQL 语句

> 原文：[`c.biancheng.net/view/7698.html`](http://c.biancheng.net/view/7698.html)

前面介绍的 mysqli_query() 函数，每次只能执行一条 SQL 命令。如果需要一次执行多条 SQL 命令，就必须使用 PHP 中的 mysqli_multi_query() 函数，函数的语法格式如下：

#### 面向对象风格的写法

mysqli::multi_query(string $query)

其中 $query 为要查询的 SQL 语句。

#### 面向过程风格的写法

mysqli_multi_query(mysqli $link, string $query)

其中 $link 为使用 mysqli_connect() 函数返回的链接标识；$query 为要查询的 SQL 语句。

参数 $query 中可以包含多条 SQL 命令，每条 SQL 命令之间使用分号`;`分隔。如果第一条 SQL 命令在执行时没有出错，那么这个函数就会返回 TRUE，否则将返回 FALSE。因为 mysqli_multi_query() 函数能够连接执行一个或多个查询，而每条 SQL 命令都可能返回一个结果，在必要时需要获取每一个结果集。所以对该函数返回结果的处理也有了一些变化，第一条查询命令的结果要用 mysqli_use_result() 或 mysqli_store_result() 函数来读取。

当然，也可以使用 mysqli_store_result() 函数将全部结果立刻取回到客户端，而且这么做的效率更高。另外，可以使用 mysqli_more_results() 函数检查是否还有其他结果集。如果想对下一个结果集进行处理，可以使用 mysqli_next_result() 函数获取下一个结果集，有下一个结果集时该函数返回 TRUE，没有时返回 FALSE，在有下一个结果集的情况下，也需要使用  mysqli_use_result() 或 mysqli_store_result() 函数来读取结果集的内容。

【示例】使用 mysqli_multi_query() 函数执行多条 SQL 命令。

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
        $sql    = 'select id,name from user;';  // SQL 语句
        $sql    .= 'select sex,age from user';  // SQL 语句
        if($mysql -> multi_query($sql)){
            do{
                if ($result = $mysql -> store_result()) {
                    while ($row = $result->fetch_row()) {
                        print_r($row);
                    }
                    $result->free();
                }
                if ($mysql -> more_results()) {
                    echo '<hr>';
                }else{
                    break;
                }
            } while ($mysql -> next_result());
        }
        $mysql -> close();
    }
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
        $sql    = 'select id,name from user;';  // SQL 语句
        $sql    .= 'select sex,age from user';  // SQL 语句
        $result = mysqli_multi_query($link, $sql);        // 执行 SQL 语句，并返回结果
        do{
            if($data = mysqli_use_result($link)){
                while ($row = mysqli_fetch_row($data)) {
                    print_r($row);
                }
                mysqli_free_result($data);
            }
            if(mysqli_more_results($link)){
                echo '<hr>';
            }else{
                break;
            }
        } while (mysqli_next_result($link));
        mysqli_close($link);
    }else{
        echo '数据库连接失败！';
    }
?>
```

运行结果如下：

Array ( [0] => 1 [1] => 张三 ) Array ( [0] => 2 [1] => 李四 ) Array ( [0] => 3 [1] => 王五 ) Array ( [0] => 4 [1] => 小明 ) Array ( [0] => 5 [1] => 小红 )
Array ( [0] => 男 [1] => 12 ) Array ( [0] => 男 [1] => 11 ) Array ( [0] => 男 [1] => 13 ) Array ( [0] => 女 [1] => 12 ) Array ( [0] => 女 [1] => 11 )