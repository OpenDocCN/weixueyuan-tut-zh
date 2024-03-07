# Guava Cache 本地缓存介绍及使用

> 原文：[`c.biancheng.net/view/5552.html`](http://c.biancheng.net/view/5552.html)

Guava Cache 是一个全内存的本地缓存实现，它提供了线程安全的实现机制。整体上来说 Guava Cache 是本地缓存的不二之选，因为其简单易用，性能好。Guava Cache 不是一个单独的缓存框架，而是 Guava 中的一个模块。

Guava Cache 的优点体现在三个方面：

*   本地缓存，读取效率高，不受网络因素影响。
*   拥有丰富的功能，操作简单。
*   线程安全。

Guava Cache 的不足之处也体现在三个方面：

*   缓存为本地缓存，不能持久化数据。
*   单机缓存，受机器内存限制，当应用重启数据时会丢失。
*   分布式部署时无法保证数据的一致性。

## 代码示例

下面我们通过 CacheBuilder 构建一个缓存对象，设置写入数据 1 分钟后过期，在 load 方法中加载数据库的数据，然后通过 CacheBuilder 对象的 get 方法获取数据。

如果缓存中存在数据则从缓存中获取数据返回，如果缓存中不存在对应的数据则执行 load 中的逻辑，从数据库中查询数据并缓存，见代码如下。

```

LoadingCache<String, Person> cahceBuilder = CacheBuilder.newBuilder()
    .expireAfterWrite(1, TimeUnit.MINUTES)
    .build(new CacheLoader<String, Person>() {
    @Override
    public Person load(String key) throws Exception {
        return dao.findById(id);
    }
});

public Person get(String id) throws Exception {
    return cahceBuilder.get(id);
}
```

## 回收策略

Guava Cache 是本地缓存，对于数据的缓存一定要有限制，不能盲目缓存数据，我们可以通过配置一些回收策略来进行缓存的回收。

CacheBuilder 为基于时间的回收提供了两种方式，具体代码如下所示。

*   expireAfterAccess(long，TimeUnit)：当缓存项在指定的时间段内没有被读或写就会被回收。这种回收策略类似于基于容量回收策略。
*   expireAfterWrite(long，TimeUnit)：当缓存项在指定的时间段内没有更新就会被回收。如果我们认为缓存数据在一段时间后不再可用，那么就可以使用该种策略。

CacheBuilder.newBuilder()
    .expireAfterWrite(1, TimeUnit.MINUTES)
    .expireAfterAccess(1, TimeUnit.MINUTES)

CacheBuilder 提供了显示移除的三种方式：

*   CacheBuilder.invalidate(key) 单个移除
*   CacheBuilder.invalidteAll(keys) 批量移除
*   CacheBuilder.invalidateAll() 移除全部

CacheBuilder 还提供了通过设置最大容量来进行移除的方式，当超出指定的容量后缓存将尝试回收最近没有使用或总体上很少使用的缓存项，见如下代码所示。

CacheBuilder.newBuilder().maximumSize(10)