# Python 操作 Redis 数据库

> 原文：[`www.weixueyuan.net/python/redis/`](http://www.weixueyuan.net/python/redis/)

传统的数据库是以表为单位存储数据的。近几年流行一些其他类型的数据库，如存储键值的数据库。我们可以将键值数据库看作是一种特殊的关系数据库，其表仅有两列，一列是键，一列是值。

比较典型的存储键值的数据库是 Memcached 和 Redis。Memcached 出现的比较早，Redis 可以看作是升级版的 Memcached。

本章主要讲解 Redis 数据库，包括服务器端和客户端的安装、各类型数据的操作等。