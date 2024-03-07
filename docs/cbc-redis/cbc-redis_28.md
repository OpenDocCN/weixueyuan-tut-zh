# 从 RedisTemplate 中获得 Jedis 实例

> 原文：[`c.biancheng.net/view/4589.html`](http://c.biancheng.net/view/4589.html)

在很多时候，我们也许需要使用一些更为高级的缓存服务器的 API，如 Redis 的流水线、事务和 Lua 语言等，所以也许会使用到 RedisTemplate 本身。

这里再多给几个实例帮助大家加深对 RedisTemplate 使用的理解。首先，定义 RedisTemplateService 的接口，代码如下所示。

```

package com.service;

public interface RedisTemplateService {
    /**
     * 执行多个命令
     */
    public void execMultiCommand();

    /**
     * 执行 Redis 事务
     */
    public void execTransaction();

    /**
     * 执行 Redis 流水线
     */
    public void execPipeline();
}
```

这样就可以提供一个实现类来展示如何使用这些方法了，代码如下所示。

```

package com.service.impl;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.dao.DataAccessException;
import org.springframework.data.redis.core.RedisOperations;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.core.SessionCallback;
import org.springframework.stereotype.Service;

import com.service.RedisTemplateService;

@Service
public class RedisTemplateServiceImpl implements RedisTemplateService {
    @Autowired
    private RedisTemplate redisTemplate = null;

    /**
     * 使用 Sessioncallback 接口实现多个命令在一个 Redis 连接中执行
     */
    @Override
    public void execMultiCommand() {
        // 使用 Java 8 lambda 表达式
        Object obj = redisTemplate.execute((RedisOperations ops) -> {
            ops.boundValueOps("key1").set("abc");
            ops.boundHashOps("hash").put("hash-key-1", "hash-value-1");
            return ops.boundValueOps("key1").get();
        });
        System.err.println(obj);
    }

    /**
     * 使用 SessionCallback 接口实现事务在一个 Redis 连接中执行
     */
    @Override
    public void execTransaction() {
        List list = (List) redisTemplate.execute((RedisOperations ops) -> {
            // 监控
            ops.watch("key1");
            // 开启事务
            ops.multi();
            // 注意，命令都不会被马上执行，只会放到 Redis 的队列屮，只会返回为 null
            ops.boundValueOps("key1").set("abc");
            ops.boundHashOps("hash").put("hash-key-1", "hash-value-1");
            ops.opsForValue().get("key1");
            // 执行 exec 方法后会触发事务执行，返回结果，存放到 list 屮
            List result = ops.exec();
            return result;
        });
        System.err.println(list);
    }

    /**
     * 执行流水线，将多个命令一次性发送给 Redis 服务器
     */
    @Override
    public void execPipeline() {
        // 使用匿名类实现
        List list = redisTemplate.executePipelined(new SessionCallback() {
            @Override
            public Object execute(RedisOperations ops) throws DataAccessException {
                // 在流水线下，命令不会马上返回结果，结果是一次性执行后返回的
                ops.opsForValue().set("key1", "value1");
                ops.opsForHash().put("hash", "key-hash-1", "value-hash-1");
                ops.opsForValue().get("key1");
                return null;
            };
        });
        System.err.println(list);
    }
}
```

执行多个命令都会用到 SessionCallback 接口，这里可以使用 Java 8 的 Lambda 表达式或者 SessionCallback 接口的匿名类，而事实上也可以使用 RedisCallback 接口，但是它会涉及底层的 API，使用起来比较困难。

因此在大多数情况下，笔者建议优先使用 SessionCallback 接口进行操作，它会提供高级 API，简化编程。

因为对于 RedisTemplate 每执行一个方法，就意味着从 Redis 连接池中获取一条连接，使用 SessionCallBack 接口后，就意味着所有的操作都来自同一条 Redis 连接，而避免了命令在不同连接上执行。

因为事务或者流水线执行命令都是先缓存到一个队列里，所以执行方法后并不会马上返回结果，结果是通过最后的一次性执行才会返回的，这点在使用的时候要注意。

在需要保证数据一致性的情况下，要使用事务。在需要执行多个命令时，可以使用流水线，它让命令缓存到一个队列，然后一次性发给 Redis 服务器执行，从而提高性能。