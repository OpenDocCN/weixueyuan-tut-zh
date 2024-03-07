# MySQL AVG 函数：求平均值

> 原文：[`c.biancheng.net/mysql/avg.html`](http://c.biancheng.net/mysql/avg.html)

MySQL AVG() 函数通过计算返回的行数和每一行数据的和，求得指定列数据的平均值。

这里需要用到以下在介绍 MAX() 函数时创建的表 tb_students_score 。

【实例】在 tb_students_score 表中，查询所有学生成绩的平均值，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT AVG(student_score)
    -> AS score_avg
    -> FROM tb_students_score;
+-----------+
| score_avg |
+-----------+
|   94.2000 |
+-----------+
1 row in set (0.03 sec)
```

> 提示：使用 AVG() 函数时，参数为要计算的列名称，若要得到多个列的平均值，则需要在每一列都使用 AVG() 函数。