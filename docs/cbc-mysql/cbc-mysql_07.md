# Mysql 字符集|为 Mysql 选择合适的字符集

## 字符集概述

字符集是一套符号和编码的规则，不论是在 oracle 数据库还是在 mysql 数据库，都存在字符集的选择问题，而且如果在数据库创建阶段没有正确选择字符集，那么可能在后期需要更换字符集，而字符集的更换是代价比较高的操作，也存在一定的风险，所以，我们推荐在应用开始阶段，就按照需求正确的选择合适的字符集，避免后期不必要的调整。

## Mysql 支持的字符集简介

mysql 服务器可以支持多种字符集（可以用 show character set 命令查看所有 mysql 支持的字符集），在同一台服务器、同一个数据库、甚至同一个表的不同字段都可以指定使用不同的字符集，相比 oracle 等其他数据库管理系统，在同一个数据库只能使用相同的字符集，mysql 明显存在更大的灵活性。

mysql 的字符集包括字符集（CHARACTER）和校对规则（COLLATION）两个概念。字符集是用来定义 mysql 存储字符串的方式，校对规则则是定义了比较字符串的方式。字符集和校对规则是一对多的关系, MySQL 支持 30 多种字符集的 70 多种校对规则。

每个字符集至少对应一个校对规则。可以用 SHOW COLLATION LIKE 'utf8%';命令查看相关字符集的校对规则。

## Unicode 简述

Unicode 是一种编码规范。我们在这里简述一下 Unicode 编码产生的历史。

先从 ASCII 码说起，ASCII 码也是一种编码规范，只不过 ASCII 码只能最多表示 256 个字符，是针对英文产生的，而面对中文、阿拉伯文之类的复杂文字，256 个字符显然是不够用的。于是各个国家或组织都相继制定了符合自己语言文字的标准，比如 gb2312、big5 等等。但是这种各自制定自己的标准的做法显然是有很多弊端的，于是 Unicode 编码规范应运而生。
Unicode 也是一种字符编码方法，不过它是由国际组织设计，可以容纳全世界所有语言文字的编码方案。Unicode 的学名是"Universal Multiple-Octet Coded Character Set"，简称为 UCS。UCS 可以看作是"Unicode Character Set"的缩写。

Unicode 有两套标准 UCS-2 和 UCS-4，前者用 2 个字节表示一个字符，后者用 4 个字节表示一个字符。以目前常用的 UCS-2 为例，它可以表示的字符数为 2¹⁶=65535，基本上可以容纳所有的欧美字符和绝大多数亚洲字符。

## 怎样选择合适的字符集

我们建议在能够完全满足应用的前提下，尽量使用小的字符集。因为更小的字符集意味着能够节省空间、减少网络传输字节数，同时由于存储空间的较小间接的提高了系统的性能。

有很多字符集可以保存汉字，比如 utf8、gb2312、gbk、latin1 等等，但是常用的是 gb2312 和 gbk。因为 gb2312 字库比 gbk 字库小，有些偏僻字（例如：洺）不能保存，因此在选择字符集的时候一定要权衡这些偏僻字在应用出现的几率以及造成的影响，不能做出肯定答复的话最好选用 gbk。

## Mysql 字符集的设置

mysql 的字符集和校对规则有 4 个级别的默认设置：服务器级、数据库级、表级和字段级。分别在不同的地方设置，作用也不相同。

服务器字符集和校对，在 mysql 服务启动的时候确定。可以在 my.cnf 中设置：
    [mysqld]
    default-character-set=utf8
或者在启动选项中指定：
    mysqld --default-character-set=utf8
或者在编译的时候指定：
    ./configure --with-charset=utf8
如果没有特别的指定服务器字符集，默认使用 latin1 作为服务器字符集。上面三种设置的方式都只指定了字符集，没有指定校对规则，这样是使用该字符集默认的校对规则，如果要使用该字符集的非默认校对规则，则需要在指定字符集的同时指定校对规则。

可以用 show variables like 'character_set_server';命令查询当前服务器的字符集和校对规则。