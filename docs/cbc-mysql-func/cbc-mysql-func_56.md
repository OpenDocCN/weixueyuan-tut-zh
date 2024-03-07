# MySQL COUNT 函数：统计查询结果的行数

> 原文：[`c.biancheng.net/mysql/count.html`](http://c.biancheng.net/mysql/count.html)

MySQL COUNT() 函数统计数据表中包含的记录行的总数，或者根据查询结果返回列中包含的数据行数，使用方法有以下两种：

*   COUNT(*) 计算表中总的行数，无论某列有数值或者为空值。
*   COUNT（字段名）计算指定列下总的行数，计算时将忽略空值的行。

这里需要用到以下在介绍 MAX() 函数时创建的表 tb_students_score 。

【实例】查询 tb_students_score 表中总的行数，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT COUNT(student_name)
    -> AS students_number
    -> FROM tb_students_score;
+-----------------+
| students_number |
+-----------------+
|              10 |
+-----------------+
1 row in set (0.03 sec)
```

由查询结果可以看到，COUNT(*) 返回 tb_students_score 表中记录的总行数，无论值是什么。返回的总数的名称为 students_number。

> 提示：在计算总数的时候对待 NULL 值的方式是，指定列的值为空的行被 COUNT() 函数忽略，但若不指定列，而在 COUNT() 函数中使用星号“*”，则所有记录都不忽略。