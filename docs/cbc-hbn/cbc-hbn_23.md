# Hibernate 事务的配置

> 原文：[`c.biancheng.net/view/4224.html`](http://c.biancheng.net/view/4224.html)

在 Hibernate 中，可以通过代码操作管理事务，例如 Transaction tx=session.beginTransaction()；表示开启一个事务；进行持久化操作后，执行 tx.commit()；操作提交事务；如果在操作的过程中出现了异常的情况，则执行 tx.rollback()；操作回滚事务。

除了使用代码对事务的开启、提交和回滚进行操作以外，还可以在 Hibernate 的配置文件中对事务进行配置。在配置文件中，可以选择使用本地事务或者全局事务，还可以设置事务的隔离级别。其具体的配置方式如下所示：

<!--使用本地事务-->
<property name= "hibernate.current_session_context_class"> thread</property>
<!--使用全局事务-->
<property name= "hibernate.current_session_context_class">jta</property>
<!--设置事务隔离级别-->
<property name= "hibernate.connection.isolation">2</property>

在上述配置代码中，使用 hibernate.current_session_context_class 参数配置本地事务和全局事务。其中，本地事务是指对一个数据库进行的操作，即只针对一个事务性资源进行操作；而全局事务是指由应用服务器管理的事务，它需要使用 JTA（Java Transaction API），可以用于多个事务性资源（跨多个数据库）。

由于 JTA 的 API 非常笨重，一般只在应用服务器的环境中使用，并且全局事务的使用限制了应用代码的重用性，所以 Hibernate 的事务管理通常会选择使用本地事务。

在上述配置中，还使用了 hibernate.connection.isolation 参数配置事务的隔离级别，并将事务的隔离级别设置为 2，表示读已提交。在 Hibernate 中，使用数字表示不同的隔离级别，它与数据库中的隔离级别相同，具体对应关系如下。

*   1—Read uncommitted 读未提交。
*   2—Read committed 读已提交。
*   4—Repeatable read 可重复读。
*   8—Serializable 串行化。