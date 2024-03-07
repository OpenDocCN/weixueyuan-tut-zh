# PHP 数组函数汇总

> 原文：[`c.biancheng.net/view/7982.html`](http://c.biancheng.net/view/7982.html)

在 PHP 开发过程中，有相当大的部分是与数组打交道，例如入库的内容、统计相关内容等操作都离不开数组，很多信息都是用数组作为载体的。所以数组的操作在 PHP 编程中占有很大的比重，只有熟练地操作数组才能更好的编写 PHP 程序。

除了前面我们介绍到的一些函数外，PHP 中还提供了很多特定功能的数组函数，本节我们就来归纳一下 PHP 中常用的数组处理函数。（有兴趣的同学可以访问 [`www.php.net/manual/zh/ref.array.php`](https://www.php.net/manual/zh/ref.array.php) 来查看更详细的内容）

数组的键/值操作：

*   array_keys()：获得数组中的键名
*   array_values()：获取数组中所有元素的值
*   array_flip()：交换数组中的键和值
*   array_key_exists()：检测键名是否位于数组中
*   array_search()：在数组中搜索给定的值并返回键名或索引

数组的拆分、合并：

*   array_slice()：截取数组的一部分
*   array_merge()：数组合并
*   array_chunk()：分割数组

数组的填充与清除：

*   array_splice()：删除数组中的某一部分并用其它值取代
*   array_pad()：用给定的值填充数组
*   array_push()：在数组尾部插入元素
*   array_pop()：删除数组末尾的元素
*   array_shift()：删除数组开头的元素
*   array_unshift()：在数组开头插入元素
*   array_fill()：以填充数据的方式创建新数组
*   array_fill_keys()：使用指定的键和值来填充数组

数组的计算：

*   array_sum()：计算数组中所有元素的和
*   array_product()：计算数组中所有元素的乘积

其它数组函数：

*   is_array()：判断是否为数组
*   array_rand()：随机获取数组元素
*   shuffle()：随机打乱数组