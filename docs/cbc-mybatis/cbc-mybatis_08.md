# MyBatis 执行 SQL 的两种方式：SqlSession 和 Mapper 接口

> 原文：[`c.biancheng.net/view/4327.html`](http://c.biancheng.net/view/4327.html)

本节主要介绍 MyBatis 执行 SQL 语句的两种方式和它们的区别。

## SqlSession 发送 SQL

有了映射器就可以通过 SqlSession 发送 SQL 了。我们以 getRole 这条 SQL 为例看看如何发送 SQL。

Role role = (Role)sqlSession.select("com.mybatis.mapper.RoleMapper.getRole",1L);

selectOne 方法表示使用查询并且只返回一个对象，而参数则是一个 String 对象和一个 Object 对象。这里是一个 long 参数，long 参数是它的主键。

String 对象是由一个命名空间加上 SQL id 组合而成的，它完全定位了一条 SQL，这样 MyBatis 就会找到对应的 SQL。如果在 MyBatis 中只有一个 id 为 getRole 的 SQL，那么也可以简写为：

Role role = (Role)sqlSession.selectOne("getRole",1L);

这是 MyBatis 前身 iBatis 所留下的方式。

## 用 Mapper 接口发送 SQL

SqlSession 还可以获取 Mapper 接口，通过 Mapper 接口发送 SQL，如下所示。

RoleMapper roleMapper = sqlSession.getMapper(RoleMapper.class);
Role role = roleMapper.getRole(1L);

通过 SqlSession 的 getMapper 方法来获取一个 Mapper 接口，就可以调用它的方法了。因为 XML 文件或者接口注解定义的 SQL 都可以通过“类的全限定名+方法名”查找，所以 MyBatis 会启用对应的 SQL 进行运行，并返回结果。

## 对比两种发送 SQL 方式

上面分别展示了 MyBatis 存在的两种发送 SQL 的方式，一种用 SqlSession 直接发送，另外一种通过 SqlSession 获取 Mapper 接口再发送。笔者建议采用 SqlSession 获取 Mapper 的方式，理由如下：

使用 Mapper 接口编程可以消除 SqlSession 带来的功能性代码，提高可读性，而 SqlSession 发送 SQL，需要一个 SQL id 去匹配 SQL，比较晦涩难懂。使用 Mapper 接口，类似 roleMapper.getRole（1L）则是完全面向对象的语言，更能体现业务的逻辑。

使用 Mapper.getRole（1L）方式，IDE 会提示错误和校验，而使用 sqlSession.selectOne（“getRole”,1L）语法，只有在运行中才能知道是否会产生错误。

目前使用 Mapper 接口编程已成为主流，尤其在 Spring 中运用 MyBatis 时，Mapper 接口的使用就更为简单，所以本教程使用 Mapper 接口的方式讨论 MyBatis。