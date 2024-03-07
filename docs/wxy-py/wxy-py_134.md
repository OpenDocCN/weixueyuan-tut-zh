# Python Redis 设置过期时间

> 原文：[`www.weixueyuan.net/a/780.html`](http://www.weixueyuan.net/a/780.html)

默认情况下，某个键的存在是没有时效性的，即在明确删除该键之前，该键会一直有效。

当然在创建字符串数据时是可以指定时效的，也可以在键创建后修改时效属性，如让该键的有效期为 5 秒，到了时间该键便会自动被删除。

下面的代码演示了一个键在设定时效后自动被删除的情况：

```

import time, redis
r = redis.Redis(host='10.20.17.13', port=6379, db=0)    # 建立连接
_key = "exists_test_key"        # 键
_val = "exists_test_val1"        # 值
if r.exists(_key) > 0:            # 如果存在了这个键，先删除它
    r.delete(_key)
r.set(_key, _val)                # 创建这个键
if r.exists(_key) > 0:            # 如果有这个键
    print("1) key exists")
r.expire(_key, 2)                   # 这个键在 2 秒后就无效了
time.sleep(3)                    # 休眠 3 秒
if r.exists(_key) > 0:            # 查看这个键是否还存在
    print("2) key exists")
else:
    print("2) key doese NOT exists")
```

运行结果：

$ python expire1.py
1) key exists                    # 刚刚创建时是存在的
2) key doese NOT exists            # 休眠 3 秒后就不存在了