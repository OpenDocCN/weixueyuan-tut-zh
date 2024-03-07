# MySQL MIN 函数：查询指定列的最小值

> 原文：[`c.biancheng.net/mysql/min.html`](http://c.biancheng.net/mysql/min.html)

MySQL MIN() 函数是用来返回查询列中的最小值。

为了便于理解，需要用到在上一节讲 MAX() 函数时创建的数据表 tb_students_score。

【实例】在 tb_students_score 表中查找最低的成绩，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT MIN(student_score)
    -> AS min_score
    -> FROM tb_students_score;
+-----------+
| min_score |
+-----------+
|        88 |
+-----------+
1 row in set (0.00 sec)
```

由结果可以看到，MIN() 函数查询出 student_score 字段的最小值为 88。

> 提示：MIN() 函数与 MAX() 函数类似，不仅适用于查找数值类型，也可应用于字符类型。