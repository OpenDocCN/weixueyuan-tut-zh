# Redis 的两种备份（持久化）方式：RDB 和 AOF

> 原文：[`c.biancheng.net/view/4560.html`](http://c.biancheng.net/view/4560.html)

在 Redis 中存在两种方式的备份：一种是快照恢复（RDB），通过快照（snapshotting）实现的，它是备份当前瞬间 Redis 在内存中的数据记录。

另一种是只追加文件（Append-Only File，AOF），其作用就是当 Redis 执行写命令后，在一定的条件下将执行过的写命令依次保存在 Redis 的文件中，将来就可以依次执行那些保存的命令恢复 Redis 的数据了。

## RDB 备份

对于快照备份而言，如果当前 Redis 的数据量大，备份可能造成 Redis 卡顿，但是恢复重启是比较快速的；对于 AOF 备份而言，它只是追加写入命令，所以备份一般不会造成 Redis 卡顿，但是恢复重启要执行更多的命令，备份文件可能也很大，使用者使用的时候要注意。

在 Redis 中允许使用其中的一种、同时使用两种，或者两种都不用，所以具体使用何种方式进行备份和持久化是用户可以通过配置决定的。对于 Redis 而言，它的默认配置为：

################################## SNAPSHOTTING###################################
......
save 900 1
save 300 10
save 60 10000
......
stop-writes-on-bgsave-error yes
......
rdbcompression yes
......
dbfilename dump.rdb
############################## APPEND ONLY MODE ###############################
......
appendonly no
......
appendfilename "appendonly.aof"
......
#appendfsync always
appendfsync everysec
# appendfsync no......
......
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
......
aof-load-truncated yes
......

对于快照模式的备份而言，它的配置项如下：

save 900 1
save 300 10
save 60 10000

这 3 个配置项的含义分别为：

*   当 900 秒执行 1 个写命令时，启用快照备份。
*   当 300 秒执行 10 个写命令时，启用快照备份。
*   当 60 秒内执行 10000 个写命令时，启用快照备份。

Redis 执行 save 命令的时候，将禁止写入命令。

stop-writes-on-bgsave-error yes

这里先谈谈 bgsave 命令，它是一个异步保存命令，也就是系统将启动另外一条进程，把 Redis 的数据保存到对应的数据文件中。它和 save 命令最大的不同是它不会阻塞客户端的写入，也就是在执行 bgsave 的时候，允许客户端继续读/写 Redis。

在默认情况下，如果 Redis 执行 bgsave 失败后，Redis 将停止接受写操作，这样以一种强硬的方式让用户知道数据不能正确的持久化到磁盘，否则就会没人注意到灾难的发生，如果后台保存进程重新启动工作了，Redis 也将自动允许写操作。然而如果安装了靠谱的监控，可能不希望 Redis 这样做，那么你可以将其修改为 no。

rdbcompression yes

这个命令意思是是否对 rbd 文件进行检验，如果是将对 rdb 文件检验。从 dbfilename 的配置可以知道，rdb 文件实际是 Redis 持久化的数据文件。

dbfilename dump.rdb

它是数据文件。当采用快照模式备份（持久化）时，Redis 将使用它保存数据，将来可以使用它恢复数据。

appendonly no

## AOF 备份

如果 appendonly 配置为 no，则不启用 AOF 方式进行备份。如果 appendonly 配置为 yes，则以 AOF 方式备份 Redis 数据，那么此时 Redis 会按照配置，在特定的时候执行追加命令，用以备份数据。

appendfilename "appendonly.aof"

这里定义追加的写入文件为 appendonly.aof，采用 AOF 追加文件备份的时候命令都会写到这里。

#appendfsync always
appendfsync everysec
# appendfsync no......

AOF 文件和 Redis 命令是同步频率的，假设配置为 always，其含义为当 Redis 执行命令的时候，则同时同步到 AOF 文件，这样会使得 Redis 同步刷新 AOF 文件，造成缓慢。而采用 evarysec 则代表每秒同步一次命令到 AOF 文件。

采用 no 的时候，则由客户端调用命令执行备份，Redis 本身不备份文件。对于采用 always 配置的时候，每次命令都会持久化，它的好处在于安全，坏处在于每次都持久化性能较差。

采用 evarysec 则每秒同步，安全性不如 always，备份可能会丢失 1 秒以内的命令，但是隐患也不大，安全度尚可，性能可以得到保障。采用 no，则性能有所保障，但是由于失去备份，所以安全性比较差。笔者建议采用默认配置 everysec，这样在保证性能的同时，也在一定程度上保证了安全性。

no-appendfsync-on-rewrite no

它指定是否在后台 AOF 文件 rewrite（重写）期间调用 fsync，默认为 no，表示要调用 fsync（无论后台是否有子进程在刷盘）。Redis 在后台写 RDB 文件或重写 AOF 文件期间会存在大量磁盘 I/O，此时，在某些 Linux 系统中，调用 fsync 可能会阻塞。

auto-aof-rewrite-percentage 100

它指定 Redis 重写 AOF 文件的条件，默认为 100，表示与上次 rewrite 的 AOF 文件大小相比，当前 AOF 文件增长量超过上次 AOF 文件大小的 100% 时，就会触发 background rewrite。若配置为 0，则会禁用自动 rewrite。

auto-aof-rewrite-min-size 64mb

它指定触发 rewrite 的 AOF 文件大小。若 AOF 文件小于该值，即使当前文件的增量比例达到 auto-aof-rewrite-percentage 的配置值，也不会触发自动 rewrite。即这两个配置项同时满足时，才会触发 rewrite。

aof-load-truncated yes

Redis 在恢复时会忽略最后一条可能存在问题的指令，默认为 yes。即在 AOF 写入时，可能存在指令写错的问题（突然断电、写了一半），这种情况下 yes 会 log 并继续，而 no 会直接恢复失败。

以上就是关于备份的全部配置内容，Redis 还有一些其他常用的配置，教程后面会介绍它们。