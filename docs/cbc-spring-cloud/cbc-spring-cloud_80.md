# 防止缓存雪崩方案

> 原文：[`c.biancheng.net/view/5556.html`](http://c.biancheng.net/view/5556.html)

缓存雪崩就是在某一时刻，大量缓存同时失效导致所有请求都去查询数据库，导致数据库压力过大，然后挂掉的情况。缓存穿透比较严重的时候也会导致缓存雪崩的发生。

## 缓存雪崩的危害

缓存雪崩最乐观的情况是存储层能抗住，但是用户体验会受到影响，数据返回慢，当压力过大时会导致存储层直接挂掉，整个系统都受影响。对于要做到 99.99% 高可用的产品，是绝对不允许缓存雪崩的发生。

## 解决方案

这里总结了几种解决方案：

1）缓存存储高可用。比如 Redis 集群，这样就能防止某台 Redis 挂掉之后所有缓存丢失导致的雪崩问题。

2）缓存失效时间要设计好。不同的数据有不同的有效期，尽量保证不要在同一时间失效，统一去规划有效期，让失效时间分布均匀即可。

3）对于一些热门数据的持续读取，这种缓存数据也可以采取定时更新的方式来刷新缓存，避免自动失效。

4）服务限流和接口限流。如果服务和接口都有限流机制，就算缓存全部失效了，但是请求的总量是有限制的，可以在承受范围之内，这样短时间内系统响应慢点，但不至于挂掉，影响整个系统。

5）从数据库获取缓存需要的数据时加锁控制，本地锁或者分布式锁都可以。当所有请求都不能命中缓存，这就是我们之前讲的缓存穿透，这时候要去数据库中查询，如果同时并发的量大，也是会导致雪崩的发生，我们可以在对数据库查询的地方进行加锁控制，不要让所有请求都过去，这样可以保证存储服务不挂掉。

## 代码示例

这里对加锁的方式进行代码讲解，代码如下所示。

```

public Person get(String id) {
    Person person = repo.findOne(id);
    if (person != null) {
        return person;
    }
    synchronized (this) {
        person = dao.findById(id);
        repo.save(person);
    }
    return person;
}
```

上面是最简单的方式，直接用代码同步块来加锁，当然我们也可以用 Lock 来加锁，加锁的本质还是控制并发量，不要让所有请求瞬时压到数据库上面去，加了锁就意味着性能要丢失一部分。其实我们可以用信号量来做，就是限制并发而已，信号量可以让多个线程同时操作，只要在数据库能够抗住的范围内即可。

## 分布式锁方式

加锁除了用 Jvm 提供的锁，我们还可以用分布式锁来解决缓存雪崩的问题，分布式锁常用的有两种，基于 Redis 和 Zookeeper 的实现。当你从网上搜分布式锁的时候，出来一大堆实现的文章，我个人不建议自己去实现这种功能，用开源的会好点，在这里我给大家推荐一个基于 Redis 实现的分布式锁。

Redisson 是一个在 Redis 的基础上实现的 Java 驻内存数据网格（In-Memory Data Grid）。

Redisson 不仅提供了一系列的分布式的 Java 常用对象，还提供了许多分布式服务（包括 BitSet、Set、Multimap、SortedSet、Map、List、Queue、BlockingQueue、Deque、BlockingDeque、Semaphore、Lock、AtomicLong、CountDownLatch、Publish/Subscribe、Bloom filter、Remote service、Spring cache、Executor service、Live Object service、Scheduler service）。

Redisson 提供了使用 Redis 最简单和便捷的方法。Redisson 的宗旨是促进使用者对 Redis 的关注分离（Separation of Concern），从而让使用者能够将精力更集中地放在处理业务逻辑上。

Redisson 跟 Jedis 差不多，都是用来操作 Redis 的框架，Redisson 中提供了很多封装，有信号量、布隆过滤器等。分布式锁只是其中一个，感兴趣的读者可以自行深入研究。

代码示例如下所示。

RLock lock = redisson.getLock("anyLock");
// 最常见的使用方法 lock.lock();
// 支持过期解锁功能
// 10 秒钟以后自动解锁
// 无须调用 unlock 方法手动解锁
lock.lock(10, TimeUnit.SECONDS);
// 尝试加锁, 最多等待 100 秒, 上锁以后 10 秒自动解锁
boolean res = lock.tryLock(100, 10, TimeUnit.SECONDS);
...
lock.unlock();