# MyBatis SqlSession 简介

> 原文：[`c.biancheng.net/view/4316.html`](http://c.biancheng.net/view/4316.html)

在 MyBatis 中，SqlSession 是其核心接口。在 MyBatis 中有两个实现类，DefaultSqlSession 和 SqlSessionManager。

DefaultSqlSession 是单线程使用的，而 SqlSessionManager 在多线程环境下使用。SqlSession 的作用类似于一个 JDBC 中的 Connection 对象，代表着一个连接资源的启用。具体而言，它的作用有 3 个：

*   获取 Mapper 接口。
*   发送 SQL 给数据库。
*   控制数据库事务。

先来掌握它的创建方法，有了 SqlSessionFactory 创建的 SqlSession 就十分简单了，如下所示。

SqlSession sqlSession = SqlSessionFactory.openSession();

注意，SqlSession 只是一个门面接口，它有很多方法，可以直接发送 SQL。它就好像一家软件公司的商务人员，是一个门面，而实际干活的是软件工程师。在 MyBatis 中，真正干活的是 Executor，我们会在底层看到它。

SqlSession 控制数据库事务的方法，如下所示。

```

//定义 SqlSession
SqlSession sqlSession = null;
try {
    // 打开 SqlSession 会话
    sqlSession = SqlSessionFactory.openSession();
    // some code...
    sqlSession.commit();    // 提交事务
} catch (IOException e) {
    sqlSession.rollback();  // 回滚事务
}finally{
    // 在 finally 语句中确保资源被顺利关闭
    if(sqlSession != null){
        sqlSession.close();
    }
}
```

这里使用 commit 方法提交事务，或者使用 rollback 方法回滚事务。因为它代表着一个数据库的连接资源，使用后要及时关闭它，如果不关闭，那么数据库的连接资源就会很快被耗费光，整个系统就会陷入瘫痪状态，所以用 finally 语句保证其顺利关闭。