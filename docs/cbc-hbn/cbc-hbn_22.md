# 数据库事务（Transaction）的四大特性和隔离级别

> 原文：[`c.biancheng.net/view/4220.html`](http://c.biancheng.net/view/4220.html)

Hibernate 是对 JDBC 的轻量级封装，其主要功能是操作数据库。在操作数据库的过程中，经常会遇到事务处理的问题，而对事务的管理，主要是在 Hibernate 的一级缓存中进行的。

在学习 Hibernate 的事务处理之前，先来学习一下什么是事务。

在数据库操作中，一项事务（Transaction）是由一条或多条操作数据库的 SQL 语句组成的一个不可分割的工作单元，这些操作要么都完成，要么都取消。接下来将围绕事务的特性、并发问题以及隔离级别进行讲解。

## 事务的特性

事务的定义很严格，它必须同时满足四个特性，即原子性、一致性、隔离性和持久性，也就是人们俗称的 ACID 特性，具体如下。

#### 1）原子性（Atomic）

表示将事务中所进行的操作捆绑成一个不可分割的单元，即对事务所进行的数据修改等操作，要么全部执行，要么全都不执行。

#### 2）一致性（Consistency）

表示事务完成时，必须使所有的数据都保持一致状态。

#### 3）隔离性（Isolation）

指一个事务的执行不能被其他事务干扰，即一个事务内部的操作及使用的数据对并发的其他事务是隔离的，并发执行的各个事务之间不能互相干扰。

#### 4）持久性（Durability）

持久性也称永久性（permanence），指一个事务一旦提交，它对数据库中的数据的改变就应该是永久性的。提交后的其他操作或故障不会对其有任何影响。

## 事务的隔离级别

在实际应用中，数据库中的数据是要被多个用户共同访问的，在多个用户同时操作相同的数据时，可能就会出现一些事务的并发问题，具体如下。

#### 1）脏读

指一个事务读取到另一个事务未提交的数据。

#### 2）不可重复读

指一个事务对同一行数据重复读取两次，但得到的结果不同。

#### 3）虚读/幻读

指一个事务执行两次查询，但第二次查询的结果包含了第一次查询中未出现的数据。

#### 4）丢失更新

指两个事务同时更新一行数据，后提交（或撤销）的事务将之前事务提交的数据覆盖了。

丢失更新可分为两类，分别是第一类丢失更新和第二类丢失更新。

*   第一类丢失更新是指两个事务同时操作同一个数据时，当第一个事务撤销时，把已经提交的第二个事务的更新数据覆盖了，第二个事务就造成了数据丢失。
*   第二类丢失更新是指当两个事务同时操作同一个数据时，第一个事务将修改结果成功提交后，对第二个事务已经提交的修改结果进行了覆盖，对第二个事务造成了数据丢失。

为了避免上述事务并发问题的出现，在标准的 SQL 规范中定义了四种事务隔离级别，不同的隔离级别对事务的处理有所不同。这四种事务的隔离级别如下。

#### 1）Read Uncommitted（读未提交）

一个事务在执行过程中，既可以访问其他事务未提交的新插入的数据，又可以访问未提交的修改数据。如果一个事务已经开始写数据，则另外一个事务不允许同时进行写操作，但允许其他事务读此行数据。此隔离级别可防止丢失更新。

#### 2）Read Committed（读已提交）

一个事务在执行过程中，既可以访问其他事务成功提交的新插入的数据，又可以访问成功修改的数据。读取数据的事务允许其他事务继续访问该行数据，但是未提交的写事务将会禁止其他事务访问该行。此隔离级别可有效防止脏读。

#### 3）Repeatable Read（可重复读取）

一个事务在执行过程中，可以访问其他事务成功提交的新插入的数据，但不可以访问成功修改的数据。读取数据的事务将会禁止写事务（但允许读事务），写事务则禁止任何其他事务。此隔离级别可有效防止不可重复读和脏读。

#### 4）Serializable（可串行化）

提供严格的事务隔离。它要求事务序列化执行，事务只能一个接着一个地执行，不能并发执行。此隔离级别可有效防止脏读、不可重复读和幻读。但这个级别可能导致大量的超时现象和锁竞争，在实际应用中很少使用。

一般来说，事务的隔离级别越高，越能保证数据库的完整性和一致性，但相对来说，隔离级别越高，对并发性能的影响也越大。因此，通常将数据库的隔离级别设置为 Read Committed，即读已提交数据，它既能防止脏读，又能有较好的并发性能。虽然这种隔离级别会导致不可重复读、幻读和第二类丢失更新这些并发问题，但可通过在应用程序中采用悲观锁和乐观锁加以控制。