# PHP errorCode()和 errorInfo()：PDO 中的错误处理

> 原文：[`c.biancheng.net/view/7758.html`](http://c.biancheng.net/view/7758.html)

在 PDO 中有两个获取程序中错误信息的方法，分别是 errorCode() 方法和 errorInfo() 方法。在介绍这两种方法之前，我们先来了解一下 PDO 中的错误处理模式。

## 1、PDO 的错误处理模式

PDO 中一共提供了三种不同的错误处理模式，不仅可以满足不同风格的编程，也可以调整扩展处理错误的方式。

#### 1) PDO::ERRMODE_SILENT

PDO::ERRMODE_SILENT 为默认模式。 在发生错误时 PDO 将只简单地设置错误码，不做其它任何操作。可使用 PDO::errorCode() 和 PDO::errorInfo() 两个方法来检查语句和数据库对象。

如果错误是由于调用语句对象而产生的，那么可以调用这个对象的 PDOStatement::errorCode() 或 PDOStatement::errorInfo() 方法。如果错误是由于调用数据库对象而产生的，那么可以在数据库对象上调用这两个方法。

#### 2) PDO::ERRMODE_WARNING

PDO::ERRMODE_WARNING 模式除了会设置错误码之外，PDO 还将发出一条传统的 E_WARNING 信息。在调试/测试期间如果只是想看看发生了什么问题，而不中断程序运行的话，可以使用该模式。

#### 3) PDO::ERRMODE_EXCEPTION

PDO::ERRMODE_EXCEPTION 模式除了会设置错误码之外，PDO 还将抛出一个 PDOException 异常类并设置它的属性来反射错误码和错误信息。此模式在调试期间也非常有用，因为它会有效地放大脚本中产生错误的点，从而可以非常快速地指出代码中有问题的潜在区域。

注意：如果异常导致脚本终止，则事务被自动回滚。

PDO 使用 SQL-92 SQLSTATE 来规范错误码字符串，不同 PDO 驱动程序负责将它们的本地代码映射为适当的 SQLSTATE 代码。PDO::errorCode() 方法返回一个单独的 SQLSTATE 码。如果需要更多这个错误的细节信息，PDO 还提供了一个 PDO::errorInfo() 方法来返回一个包含 SQLSTATE 码、特定驱动错误码以及此驱动的错误字符串的数组。

异常模式另一个非常有用的作用是，相比传统 PHP 风格的警告，可以更清晰地构建自己的错误处理，而且比起静默模式和显式地检查每种数据库调用的返回值，异常模式需要的代码/嵌套更少。

【示例】创建 PDO 实例，并设置错误模式。

```

<?php
    $dsn  = 'mysql:dbname=test;host=127.0.0.1';
    $user = 'root';
    $pwd  = 'root';
    try {
        $pdo = new PDO($dsn, $user, $pwd);
        $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    } catch (PDOException $e) {
        echo 'Connection failed: ' . $e->getMessage();
    }
?>
```

除了示例中介绍的使用 PDO::setAttribute() 方法设置错误模式外，还可以在创建 PDO 实例时设置错误模式，如下所示：

$pdo = new PDO($dsn, $user, $pwd, array(PDO::ATTR_ERRMODE => PDO::ERRMODE_WARNING));

## 2、PDO::errorCode() 方法

PDO::errorCode() 方法用于获取在操作数据库句柄时所发生的错误代码，这些错误代码被称为 SQLSTATE 代码。其语法格式如下：

PDO::errorCode()

PDO::errorCode() 方法可以返回一个 SQLSTATE，一个由 5 个字母或数字组成的在 ANSI SQL 标准中定义的标识符。 简要地说，一个 SQLSTATE 由前面两个字符的类值和后面三个字符的子类值组成。

【示例】通过 PDO 连接数据库，并通过 errorCode() 方法获取错误代码。

```

<?php
    $dsn  = 'mysql:dbname=test;host=127.0.0.1';
    $user = 'root';
    $pwd  = 'root';
    try {
        $pdo = new PDO($dsn, $user, $pwd);
        $sql = 'select * from user';
        $res = $pdo -> query($sql);
        echo 'errorCode 为：'.$pdo -> errorCode().'<br>';
        foreach ($res as $key => $value) {
            echo '序号：'.$value['id'].'； 姓名：'.$value['name'].'； 年龄：'.$value['age'].'； 性别：'.$value['sex'].'<br>';
        }
    } catch (PDOException $e) {
        echo 'Connection failed: ' . $e->getMessage();
    }
?>
```

运行结果如下：

errorCode 为：00000
序号：1； 姓名：张三； 年龄：12； 性别：男
序号：2； 姓名：李四； 年龄：11； 性别：男
序号：3； 姓名：王五； 年龄：12； 性别：男
序号：4； 姓名：小明； 年龄：12； 性别：女
序号：5； 姓名：小红； 年龄：11； 性别：女
序号：6； 姓名：小刚； 年龄：14； 性别：男

## 3、PDO::errorInfo() 方法

PDO::errorInfo() 方法用于获取操作数据库句柄时所发生的错误信息。其语法格式如下：

PDO::errorInfo()

errorInfo() 方法的返回值为一个数组，它包含了相关的错误信息。

【示例】使用 PDO 中的 query 方法完成数据查询操作，并通过 errorInfo() 方法获取错误信息。

```

<?php
    $dsn  = 'mysql:dbname=test;host=127.0.0.1';
    $user = 'root';
    $pwd  = 'root';
    try {
        $pdo = new PDO($dsn, $user, $pwd);
        // $pdo -> setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
        $sql = 'select * from userr';
        $res = $pdo -> query($sql);
        echo 'errorInfo 为：<br>';
        print_r($pdo -> errorInfo());
    } catch (PDOException $e) {
        echo 'Connection failed: ' . $e->getMessage();
    }
?>
```

示例中我们查询了一个不存在的数据库，运行结果为：

errorInfo 为：
Array ( [0] => 42S02 [1] => 1146 [2] => Table 'test.userr' doesn't exist )

PDO 和 PDOStatement 对象中都有 errorCode() 和 errorInfo() 方法，如果没有任何错误，errorCode() 返回的是 00000；否则就会返回一些错误代码。而 errorInfo() 返回的是一个数组，包括 PHP 定义的错误代码和 MySQL 的错误代码及错误信息。