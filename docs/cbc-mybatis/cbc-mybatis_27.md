# MyBatis 关联查询（级联查询）

> 原文：[`c.biancheng.net/view/4367.html`](http://c.biancheng.net/view/4367.html)

级联关系是一个数据库实体的概念，有 3 种级联关系，分别是一对一级联、一对多级联以及多对多级联。

级联的优点是获取关联数据十分方便，但是级联过多会增加数据库系统的复杂度，同时降低系统的性能。

在实际开发中要根据实际情况判断是否需要使用级联。更新和删除的级联关系很简单，由数据库内在机制即可完成。本节只讲述级联查询的相关实现。

如果表 A 中有一个外键引用了表 B 的主键，A 表就是子表，B 表就是父表。当查询表 A 的数据时，通过表 A 的外键将表 B 的相关记录返回，这就是级联查询。例如，当查询一个人的信息时，同时根据外键（身份证号）将他的身份证信息返回。

由于本节篇幅有限，因此分为 3 节对 MyBatis 的级联关系进行详细讲解，读者可点击下面链接进行学习：

*   MyBatis 一对一关联查询（级联查询）
*   MyBatis 一对多关联查询（级联查询）
*   MyBatis 多对多关联查询（级联查询）