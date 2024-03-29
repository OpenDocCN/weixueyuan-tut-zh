# Redis 是什么？

> 原文：[`c.biancheng.net/view/6576.html`](http://c.biancheng.net/view/6576.html)

Redis 是一个开源的、高性能的、键值对内存数据库。它通过提供多种键值数据类型来满足不同场景下的存储需求，并借助许多高层级的接口使其可以胜任如缓存、队列系统等不同的角色。

本小节将介绍 Redis 的历史和特性，以使读者能够快速地对 Redis 有一个全面的了解。

## Redis 历史与发展

2008 年，意大利的一家创业公司 Merzia 推出了一款基于 MySQL 的网站实时统计系统 —— LLOOGG，然而没过多久，该公司的创始人萨尔瓦托•桑菲利普（Salvatore Sanfilippo）便对这个系统的性能感到失望，于是他决定亲自为 LLOOGG 量身定做一个数据库，并于 2009 年开发完成， 这个数据库就是 Redis。

不过萨尔瓦托并不满足只将 Redis 用于 LLOOGG 这一款产品，而是希望让更多的人使用它，于是萨尔瓦托将 Redis 开源发布，并开始和 Redis 的另一名主要的代码贡献者皮特•诺德胡斯（Pieter Noordhuis）一起继续着 Redis 的开发，直到今天。

萨尔瓦托自己也没有想到，在短短的几年时间内，Redis 就拥有了庞大的用户群体。Hacked News 在 2012 年发布了一份数据库的使用情况调查，结果显示有近 12% 的公司在使用 Redis。

国内如新浪微博和知乎，国外如 GitHub、Stack Overflow、Flickr、暴雪和 Instagram，都是 Redis 的用户。现在使用 Redis 的用户越来越多，大多数的互联网公司都使用 Redis 作为公共缓存。

VMware 公司从 2010 年开始赞助 Redis 的开发，萨尔瓦托和皮特也分别于同年的 3 月和 5 月加入 VMware，全职开发 Redis。

## Redis 特性

#### 存储结构

有过脚本语言编程经验的读者对字典（或称映射、关联数组）数据结构一定很熟悉，如在代码 dict["key"]="value" 中，“diet”是一个字典结构变量，字符串“key”是键名，而“value”是键值，在字典中可以获取或设置键名对应的键值，也可以删除一个键。

Redis 是 Remote Dictionary Server（远程字典服务器）的缩写，它以字典结构存储数据，并允许其他应用通过 TCP 读写字典中的内容。同大多数脚本语言中的字典一样，Redis 字典中的键值除了可以是字符串，还可以是其他数据类型。

到目前为止，Redis 支持的键值数据类型有：字符串类型、散列类型、列表类型、集合类型和有序集合类型。

这种字典形式的存储结构与常见的 MySQL 等关系数据库的二维表形式的存储结构有很大的差异。举个例子，在程序中使用 post 变量存储了一篇文章的数据（包括标题、正文、阅读量和标签），如下所示：

```

post["title"] = "Hello World!"
post["content"] = "Blablabla..."
post["views"] = 0
post["tags"] = ["PHP","Ruby","Node.js"]
```

现在希望将这篇文章的数据存储在数据库中，并且要求可以通过标签检索岀文章。

如果使用关系数据库存储，一般会将其中的标题、正文和阅读量存储在一个表中，而将标签存储在另一个表中，然后使用第三个表连接文章和标签表。需要查询时还需要连接三个表，不是很直观。

而 Redis 字典结构的存储方式和对多种键值数据类型的支持使得开发者可以将程序中的数据直接映射到 Redis 中，数据在 Redis 中的存储形式和其在程序中的存储方式非常相似。

使用 Redis 的另一个优势是其对不同的数据类型提供了非常方便的操作方式，如使用集合类型存储文章标签，Redis 可以对标签进行如交集、并集等集合运算操作。

#### 内存存储与持久化

Redis 数据库中的所有数据都存储在内存中。由于内存的读写速度远高于硬盘，因此 Redis 在性能上与其他基于硬盘存储的数据库相比有非常明显的优势。在一台普通的笔记本电脑上，Redis 可以在一秒内读写超过 10 万个键值。

将数据存储在内存中也有问题，例如，程序退出后内存中的数据会丢失。不过 Redis 提供了对持久化的支持，即可以将内存中的数据异步写入硬盘中，同时不影响其继续提供服务。

#### 功能丰富

Redis 虽然是作为数据库开发的，但由于其提供了丰富的功能，越来越多的人将其用作缓存、 队列系统等。Redis 可谓是名副其实的多面手。

Redis 可以为每个键设置生存时间（Time To Live, TTL），生存时间到期后键会自动被删除。 这一功能配合出色的性能让 Redis 可以作为缓存系统来使用，而且由于 Redis 支持持久化和丰富的数据类型，使其成为另一个非常流行的缓存系统 Memcached 的有力竞争者。

讨论 Redis 和 Memcached 的优劣一直是一个热门的话题。由于 Redis 是单线程模型，而 Memcached 支持多线程，所以在多核服务器上后者的性能更高一些。

然而，前面已经介绍过，Redis 的性能已经足够优异，在绝大部分场合下其性能都不会成为瓶颈。所以在使用时更应该关心的是二者在功能上的区别，如果需要用到高级的数据类型或持久化等功能，Redis 将会是 Memcached 很好的替代品。

作为缓存系统，Redis 还可以限定数据占用的最大内存空间，在数据达到空间限制后可以按照一定的规则自动淘汰不需要的键。

除此之外，Redis 的列表类型键可以用来实现队列，支持阻塞式读取，并且可以很容易地实现一个高性能的优先级队列。同时，在更高层面上，Redis 还支持“发布/订阅”的消息模式，用户可以基于此构建聊天室等系统。

#### 简单稳定

如果一个工具使用起来太复杂，即使它的功能再丰富也很难吸引人。Redis 直观的存储结构使其通过程序与 Redis 交互十分简单。在 Redis 中使用命令来读写数据，命令语句之于 Redis 就相当于 SQL 之于关系数据库。

例如，在关系数据库中要获取 posts 表内 id 为 1 的记录的 title 字段的值，可以使用如下 SQL 语句实现：

SELECT title FROM posts WHERE id=1 LIMIT 1

相对应的，在 Redis 中要读取键名为 post:1 的散列类型键的 title 字段的值，可以使用如下命令语句实现：

HGET post : 1 title

其中，HGET 就是一个命令。Redis 提供了 100 多个命令，但是常用的只有十几个，并且每个命令都很容易记住。

Redis 提供了几十种不同编程语言的客户端库，这些库都封装了 Redis 的命令，这样在程序中与 Redis 进行交互变得很容易。

有些库还提供了可以将编程语言中的数据类型直接以相应的形式存储到 Redis 中（如将数组直接以列表类型存入 Redis）的简单方法，使用起来非常方便。

Redis 使用 C 语言开发，代码量只有 3 万多行。这降低了用户通过修改 Redis 源代码来使之更适合自己项目需要的门槛。对于希望“榨干”数据库性能的开发者而言，这无疑具有很大的吸引力。

Redis 是开源的，有将近 100 名开发者为 Redis 贡献了代码。良好的开发氛围和严谨的版本发布机制使得 Redis 的稳定版本的性能非常可靠，如此多的公司选择使用 Redis 也可以印证这 一点。

#### Memcached 与 Redis 比较

Memcached 与 Redis 的比较见下表。

Memcached 与 Redis 的比较

| 数据库 | CPU | 内存利用率 | 持久性 | 数据结构 | 工作环境 |
| Memcached | 支持多核 | 高 | 无 | 简单 | Linux/Windows |
| Redis | 单核 | 低（压缩比 Memcached 高） | 有（硬盘存储，主从同步） | 复杂 | Linux |