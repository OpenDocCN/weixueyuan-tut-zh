# Hibernate 的 5 种检索方式

> 原文：[`c.biancheng.net/view/4211.html`](http://c.biancheng.net/view/4211.html)

Hibernate 的检索方式主要有五种，包括导航对象图检索方式、OID 检索方式、HQL 检索方式、QBC 检索方式和本地 SQL 检索方式，接下来针对 Hibernate 五种检索方式进行详细讲解。

## 导航对象图检索方式

导航对象图检索方式是根据已经加载的对象，导航到其他对象。它利用类与类之间的关系检索对象。

例如，对于已经加载的学生对象，就可以利用学生对象自动导航找到该学生所对应的班级对象，但前提是需要在对象关系映射文件上配置两者多对一的关系。其检索示例代码如下所示：

Student student = (Student)session.get(Student.class,1);
Grade grade = student.getGrade();

## OlD 检索方式

OID 检索方式是指按照对象的 OID 检索对象。它使用 Session 对象的 get() 和 load() 方法加载某一条记录所对应的对象，其使用的前提是需要事先知道 OID 的值。该检索方式的示例代码如下所示：

Grade grade1 = (Grade)session.get(Grade.class,1);
Grade grade2 = (Grade)session.get(Grade.class,1);

## HQL 检索方式

HQL（Hibernate Query Language）是 Hibernate 查询语言的简称，它是一种面向对象的查询语言，与 SQL 查询语言有些类似，但它使用的是类、对象和属性的概念，而没有表和字段的概念。

HQL 查询与 SQL 查询相比，具有以下优点。

*   直接针对实体类和属性进行查询，不用再编写繁琐的 SQL 语句。
*   查询结果直接保存在 List 集合中，不用再次封装。
*   针对不同的数据库会自动生成不同的 SQL 语句。

在 Hibernate 提供的几种检索方式中，HQL 是官方推荐的查询语言，也是使用最频繁的一种检索方式，其具有以下主要功能。

*   在查询语句中设定各种查询条件。
*   支持投影查询，即仅检索出对象的部分属性。
*   提供内置聚集函数，如 sum()、min() 和 max()。
*   支持分组查询，允许使用 group by 和 having 关键字。
*   支持分页查询。
*   支持子查询，即嵌套查询。
*   支持动态绑定参数。

HQL 的语法格式与 SQL 非常相似，并且在 Hibernate 中专门为 HQL 提供了一个 Query 查询接口执行各种复杂的查询语句。HQL 的完整语法格式如下所示：

[select/update/delete...]from...[where...][group by...][having...][order by...][asc/desc]

从上述语法格式中可以看出，HQL 查询与 SQL 查询非常类似。通常情况下，当检索表中的所有数据时，查询语句中可以省略 select 关键字，其示例如下所示：

String hql="from User";

需要注意的是，上述语句中 User 表示类名，而不是表名，因此需要区分大小写，而 from 关键字不区分大小写。

## QBC 检索方式

QBC（Query By Criteria）是 Hibernate 提供的另一种检索对象的方式，它主要由 Criteria 接口、Criterion 接口和 Expression 类组成，并且支持在运行时动态生成查询语句。

QBC 查询主要由 Criteria 接口完成，该接口由 Session 对象创建，Criterion 是 Criteria 的查询条件，在 Criteria 中提供了 add（Criterion criterion）方法添加查询条件。

以查询 id 为 1 的 User 对象为例，使用 QBC 检索对象的示例代码如下所示：

```

// 创建 criteria 对象
Criteria criteria =session.createCriteria(User.class);
// 设定查询条件
Criterion criterion = Restrictions.eq("id",1);
// 添加查询条件
criteria.add(criterion);
// 执行查询，返回查询结果
List<User> gs = criteria.list ();
```

## 本地 SQL 检索方式

本地 SQL 检索方式就是使用本地数据库的 SQL 查询语句进行查询。在 Hibernate 中，SQL 查询是通过 SQLQuery 接口表示的，该接口是 Query 接口的子接口，因此可以调用 Query 接口的方法。

使用本地 SQL 检索方式检索对象的示例代码，代码如下所示：

SQLQuery sqlQuery = session.createSQLQuery("select * from user");