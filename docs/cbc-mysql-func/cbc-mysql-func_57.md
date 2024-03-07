# MySQL SUM 函数：求和

> 原文：[`c.biancheng.net/mysql/sum.html`](http://c.biancheng.net/mysql/sum.html)

MySQL SUM() 是一个求总和的函数，返回指定列值的总和。

SUM() 函数是如何工作的？

*   如果在没有返回匹配行 SELECT 语句中使用 SUM 函数，则 SUM 函数返回 NULL，而不是 0。
*   DISTINCT 运算符允许计算集合中的不同值。
*   SUM 函数忽略计算中的 NULL 值。

这里需要用到以下在介绍 MAX() 函数时创建的表 tb_students_score 。

【实例】在 tb_students_score 表中计算学生成绩的总分，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT SUM(student_score)
    -> AS score_sum
    -> FROM tb_students_score;
+-----------+
| score_sum |
+-----------+
|       942 |
+-----------+
1 row in set (0.00 sec)
```

由查询结果可以看到，SUM() 函数返回学生的所有成绩之和为 942。

> 提示：SUM() 函数在计算时，忽略列值为 NULL 的行。