# Redis 内存回收策略

> 原文：[`c.biancheng.net/view/4562.html`](http://c.biancheng.net/view/4562.html)

Redis 也会因为内存不足而产生错误，也可能因为回收过久而导致系统长期的停顿，因此掌握执行回收策略十分有必要。在 Redis 的配置文件中，当 Redis 的内存达到规定的最大值时，允许配置 6 种策略中的一种进行淘汰键值，并且将一些键值对进行回收，让我们来看看它们的特点。

首先，Redis 的配置文件放在 Redis 的安装目录下，在 Windows 中是 redis.windows.conf，在 Lunix/Unix 中则是 redis.conf。Redis 对其中的一个配置项——maxmemory-policy，提供了这样的一段描述：

# volatile-lru -> remove the key with an expire set using an LRU algorithm
# allkeys-lru -> remove any key according to the LRU algorithm
# volatile-random -> remove a random key with an expire set
# allkeys-random -> remove a random key, any key
# volatile-ttl -> remove the key with the nearest expire time (minor TTL)
# noeviction -> don't expire at all, just return an error on write operations

更深一步地阐述它们的含义。

表 1 maxmemory-policy 说明

| 名称 | 说明 |
| volatile-lru | 采用最近使用最少的淘汰策略，Redis 将回收那些超时的（仅仅是超时的）键值对，也就是它只淘汰那些超时的键值对。 |
| allkeys-lru | 采用淘汰最少使用的策略，Redis 将对所有的（不仅仅是超时的）键值对采用最近使用最少的淘汰策略。 |
| volatile-random | 采用随机淘汰策略删除超时的（仅仅是超时的）键值对。 |
| allkeys-random | 采用随机淘汰策略删除所有的（不仅仅是超时的）键值对，这个策略不常用。 |
| volatile-ttl | 采用删除存活时间最短的键值对策略。 |
| noeviction | 根本就不淘汰任何键值对，当内存已满时，如果做读操作，例如 get 命令，它将正常工作，而做写操作，它将返回错误。也就是说，当 Redis 采用这个策略内存达到最大的时候，它就只能读而不能写了。 |

Redis 在默认情况下会采用 noeviction 策略。换句话说，如果内存已满，则不再提供写入操作，而只提供读取操作。显然这往往并不能满足我们的要求，因为对于互联网系统而言，常常会涉及数以百万甚至更多的用户，所以往往需要设置回收策略。

这里需要指出的是：LRU 算法或者 TTL 算法都是不是很精确算法，而是一个近似的算法。Redis 不会通过对全部的键值对进行比较来确定最精确的时间值，从而确定删除哪个键值对，因为这将消耗太多的时间，导致回收垃圾执行的时间太长，造成服务停顿。

而在 Redis 的默认配置文件中，存在着参数 maxmemory-samples，它的默认值为 3，假设采取了 volatile-ttl 算法，让我们去了解这样的一个回收的过程，假设当前有 5 个即将超时的键值对，如表 2 所示。

表 2 volatile-ttl 样本删除方式

| 键值对 | 剩余超时秒数 | 备注 |
| A1 | 6 | 属于探测样本 |
| A2 | 3 | 属于探测样本中的最短值，因此最先删除 |
| A3 | 4 | 属于探测样本 |
| A4 | 1 | 最短值，但是它不属于探测样本，所以没有最先删除 |
| A5 | 9 | 但不属于样本 |

由于配置 maxmemory-samples 的值为 3，如果 Redis 是按表中的顺序探测，那么它只会取到样本 A1、A2、A3，然后进行比较，因为 A2 过期剩余秒数最少，所以决定淘汰 A2，因此 A2 是最先被删除的。

注意，此时即将过期且剩余超时秒数最短的 A4 却还在内存中，因为它不属于探测样本。这就是 Redis 中采用的近似算法。当设置 maxmemory-samples 越大，则 Redis 删除的就越精确，但是与此同时带来不利的是，Redis 也就需要花更多的时间去计算和匹配更为精确的值。

回收超时策略的缺点是必须指明超时的键值对，这会给程序开发带来一些设置超时的代码，无疑增加了开发者的工作量。

对所有的键值对进行回收，有可能把正在使用的键值对删掉，增加了存储的不稳定性。对于垃圾回收的策略，还需要注意的是回收的时间，因为在 Redis 对垃圾的回收期间，会造成系统缓慢。

因此，控制其回收时间有一定好处，只是这个时间不能过短或过长。过短则会造成回收次数过于频繁，过长则导致系统单次垃圾回收停顿时间过长，都不利于系统的稳定性，这些都需要设计者在实际的工作中进行思考。