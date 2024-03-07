# MySQL MAX 函数：查询指定列的最大值

> 原文：[`c.biancheng.net/mysql/max.html`](http://c.biancheng.net/mysql/max.html)

MySQL MAX() 函数是用来返回指定列中的最大值。

为了方便理解，首先创建一个学生成绩表 tb_students_score，学生成绩表的数据内容如下所示。

```

mysql> use test_db;
Database changed
mysql> SELECT * FROM tb_students_score;
+--------------+---------------+
| student_name | student_score |
+--------------+---------------+
| Dany         |            90 |
| Green        |            99 |
| Henry        |            95 |
| Jane         |            98 |
| Jim          |            88 |
| John         |            94 |
| Lily         |           100 |
| Susan        |            96 |
| Thomas       |            93 |
| Tom          |            89 |
+--------------+---------------+
10 rows in set (0.13 sec)
```

【实例 1】在 tb_students_score 表中查找最高的成绩，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT MAX(student_score)
    -> AS max_score
    -> FROM tb_students_score;
+-----------+
| max_score |
+-----------+
|       100 |
+-----------+
1 row in set (0.06 sec)
```

由运行结果可以看到，MAX() 函数查询出 student_score 字段的最大值为 100。

MAX() 函数不仅适用于查找数值类型，也可应用于字符类型。

【实例 2】在 tb_students_score 表中查找 student_name 的最大值，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT MAX(student_name)
    -> AS max_name
    -> FROM tb_students_score;
+----------+
| max_name |
+----------+
| Tom      |
+----------+
1 row in set (0.03 sec)
```

由运行结果可以看到，MAX() 函数可以对字母进行大小判断，并返回最大的字符或者字符串值。

> 注意：MAX() 函数还可以返回任意列中的最大值，包括返回字符类型的最大值。在对字符类型的数据进行比较时，按照字符的 ASCII 码值大小进行比较，从 a～z，a 的 ASCII 码最小，z 的最大。在比较时，先比较第一个字符，如果相等，继续比较下一个字符，一直到两个字符不相等或者字符结束为止。例如，b 与 t 比较时，t 为最大值；bcd 与 bca 比较时，bcd 为最大值。