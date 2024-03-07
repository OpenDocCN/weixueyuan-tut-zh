# 如何减少对 Mysql 的访问以优化 SQL 语句

### 避免对同一数据做重复检索：

应用中需要理清楚对数据库的访问逻辑，需要对相同表的访问，尽量集中在相同 sql 访问，一次提取结果，减少对数据库的重复访问。

### 使用 mysql query cache

作用：查询缓存存储 SELECT 查询的文本以及发送给客户端的相应结果。如果随后收到一个相同的查询，服务器从查询缓存中重新得到查询结果，而不再需要解析和执行查询。

适用范围：不发生数据更新的表。当表更改（包括表结构和表数据）后，查询缓存值的相关条目被清空。

**查询缓存的主要参数设置：**
show variables like ‘%query_cache%’;
have_query_cache 表明服务器在安装使已经配置了高速缓存
query_cache_size 表明缓存区大小，单位为 M
query_cache_type 的变量值从 0 到 2，含义分别为
0 或者 off（缓存关闭）
1 或者 on（缓存打开，使用 sql_no_cache 的 select 除外）
2 或者 demand（只有带 sql_cache 的 select 语句提供高速缓存）

在 SHOW STATUS 中，你可以监视查询缓存的性能：

| 变量 | 含义 |
| Qcache_queries_in_cache | 在缓存中已注册的查询数目 |
| Qcache_inserts | 被加入到缓存中的查询数目 |
| Qcache_hits | 缓存采样数数目 |
| Qcache_lowmem_prunes | 因为缺少内存而被从缓存中删除的查询数目 |
| Qcache_not_cached | 没有被缓存的查询数目 (不能被缓存的，或由于 QUERY_CACHE_TYPE) |
| Qcache_free_memory | 查询缓存的空闲内存总数 |
| Qcache_free_blocks | 查询缓存中的空闲内存块的数目 |
| Qcache_total_blocks | 查询缓存中的块的总数目 |

### 加 cache 层

Cache（高速缓存）、Memory（内存）、Hard disk（硬盘）都是数据存取单元，但存取速度却有很大差异，呈依次递减的顺序。对于 CPU 来说，它可以从距离自己最近的 Cache 高速地存取数据，而不是从内存和硬盘以低几个数量级的速度来存取数据。而 Cache 中所存储的数据，往往是 CPU 要反复存取的数据，有特定的机制（或程序）来保证 Cache 内数据的命中率（Hit Rate）。因此，CPU 存取数据的速度在应用高速缓存后得到了巨大的提高。

因为将数据写入高速缓存的任务由 Cache Manager 负责，所以对用户来说高速缓存的内容肯定是只读的。需要你做的工作很少，程序中的 SQL 语句和直接访问 DBMS 时没有分别，返回的结果也看不出有什么差别。而数据库厂商往往会在 DB Server 的配置文件中提供与 Cache 相关的参数，通过修改它们，可针对我们的应用优化 Cache 的管理。