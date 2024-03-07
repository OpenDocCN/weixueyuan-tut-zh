# PHP 使用 PDO 获取查询结果

> 原文：[`c.biancheng.net/view/7742.html`](http://c.biancheng.net/view/7742.html)

PDO 的数据获取方法与其他数据库扩展都非常类似，只要成功执行 SELECT 查询，都会有结果集对象生成。不管是使用 PDO 对象中的 query() 方法，还是使用 prepare() 和 execute() 等方法结合的预处理语句，执行 SELECT 查询都会得到结果集对象 PDOStatement。

通过 PDOStatement 类中的方法就可以获取 SELECT 语句的查询结果，下面介绍 PDOStatement 类中常见的几个获取结果集数据的方法。

## 1、fetch() 方法

fetch() 方法可以从一个 PDOStatement 对象的结果集中获取当前行的内容，并将结果集指针移至下一行，当到达结果集末尾时返回 FALSE，该方法的语法格式如下：

PDOStatement::fetch([int $fetch_style[, int $cursor_orientation = PDO::FETCH_ORI_NEXT[, int $cursor_offset = 0]]])

参数说明如下：

*   $fetch_style：可选参数，用来控制下一行如何返回给调用者。此值必须是 PDO::FETCH_* 系列常量中的一个，如下所示：
    *   PDO::FETCH_ASSOC：返回一个关联数组；
    *   PDO::FETCH_BOTH（默认）：返回一个索引数组加关联数组混合的数组；
    *   PDO::FETCH_BOUND：返回 TRUE，并分配结果集中的值给 PDOStatement::bindColumn() 方法绑定的 PHP 变量；
    *   PDO::FETCH_CLASS：返回一个请求类的新实例，映射结果集中的列名到类中对应的属性名。如果 fetch_style 包含 PDO::FETCH_CLASSTYPE（例如：PDO::FETCH_CLASS | PDO::FETCH_CLASSTYPE），则类名由第一列的值决定；
    *   PDO::FETCH_INTO：更新一个被请求类已存在的实例，映射结果集中的列到类中命名的属性；
    *   PDO::FETCH_LAZY：结合使用 PDO::FETCH_BOTH 和 PDO::FETCH_OBJ，创建供用来访问的对象变量名；
    *   PDO::FETCH_NUM：返回一个索引数组；
    *   PDO::FETCH_OBJ：返回一个属性名对应结果集列名的匿名对象。
*   $cursor orientation：可选参数，用来确定当对象是一个可滚动的游标时应当获取哪一行。此值必须是 PDO::FETCH_ORI_* 系列常量中的一个，默认为 PDO::FETCH_ORI_NEXT。要想让 PDOStatement 对象使用可滚动游标，必须在用 PDO::prepare() 预处理 SQL 语句时，设置 PDO::ATTR_CURSOR 属性为 PDO::CURSOR_SCROLL；
*   $offset：可选参数，当参数 $cursor_orientation 设置为 PDO::FETCH_ORI_ABS 时，此值指定结果集中想要获取行的绝对行号；当参数 $cursor_orientation 设置为 PDO::FETCH_ORI_REL 时，此值指定想要获取行相对于调用 PDOStatement::fetch() 前游标的位置。

【示例】使用 fetch() 方法，获取 SELECT 语句的查询结果。

```

<?php
    $dsn  = 'mysql:host=127.0.0.1;dbname=test';
    $user = 'root';
    $pwd  = 'root';
    try{
        $pdo = new PDO($dsn,$user,$pwd);
        $sql = 'SELECT name,age,sex FROM user WHERE age = \'11\'';
        $res = $pdo -> query($sql);
        echo '<pre>';
        while ($row = $res -> fetch(PDO::FETCH_ASSOC)){
            print_r($row);
        }
    }catch(PDOException $e){
        echo '数据库连接失败：'.$e -> getMessage();
    }
?>
```

运行结果如下：

Array
(
    [name] => 李四
    [age] => 11
    [sex] => 男
)
Array
(
    [name] => 小红
    [age] => 11
    [sex] => 女
)

## 2、fetchAll() 方法

fetchAll() 方法与上面介绍的 fetch() 方法类似，但是该方法只需要调用一次就可以获取结果集中的所有行，并赋给返回的数组。该方法的语法格式如下：

PDOStatement::fetchAll([int $fetch_style[, mixed $fetch_argument[, array $ctor_args = array()]]])

参数说明如下：

*   $fetch_style：可选参数，用来控制返回数组的内容，默认值为 PDO::FETCH_BOTH。该参数的取值与 fetch() 方法相同；
*   $fetch_argument：根据 $fetch_style 参数的值，此参数有不同的意义：
    *   PDO::FETCH_COLUMN：返回指定以 0 开始索引的列；
    *   PDO::FETCH_CLASS：返回指定类的实例，映射每行的列到类中对应的属性名；
    *   PDO::FETCH_FUNC：将每行的列作为参数传递给指定的函数，并返回调用函数后的结果。
*   $ctor_args：当 $fetch_style 参数为 PDO::FETCH_CLASS 时，自定义类的构造函数的参数。

【示例】使用 fetchAll() 方法，获取 SELECT 语句的查询结果。

```

<?php
    $dsn  = 'mysql:host=127.0.0.1;dbname=test';
    $user = 'root';
    $pwd  = 'root';
    try{
        $pdo  = new PDO($dsn,$user,$pwd);
        $sql  = 'SELECT name,age,sex FROM user WHERE sex = \'女\'';
        $res  = $pdo -> query($sql);
        $data = $res -> fetchAll(PDO::FETCH_ASSOC);
        echo '<pre>';
        print_r($data);
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
            [name] => 小明
            [age] => 12
            [sex] => 女
        )
    [1] => Array
        (
            [name] => 小红
            [age] => 11
            [sex] => 女
        )
)

## 3、fetchColumn() 方法

fetchColumn() 方法可以获取结果集中当前行指定字段的值，其语法格式如下：

PDOStatement::fetchColumn([int $column_number = 0])

其中参数 $column_number 为想从行里取回的列的索引数字（以 0 开始）。如果没有提供值，则获取第一列。

【示例】使用 fetchColumn() 方法，获取指定字段的值。

```

<?php
    $dsn  = 'mysql:host=127.0.0.1;dbname=test';
    $user = 'root';
    $pwd  = 'root';
    try{
        $pdo = new PDO($dsn,$user,$pwd);
        $sql = 'SELECT name,age,sex FROM user';
        $res = $pdo -> query($sql);
        echo '当前行第一列的值为：'.$res -> fetchColumn().'<br>';
        echo '当前行第三列的值为：'.$res -> fetchColumn(2).'<br>';
        echo '当前行第二列的值为：'.$res -> fetchColumn(1).'<br>';
    }catch(PDOException $e){
        echo '数据库连接失败：'.$e -> getMessage();
    }
?>
```

运行结果如下：

当前行第一列的值为：张三
当前行第三列的值为：男
当前行第二列的值为：12