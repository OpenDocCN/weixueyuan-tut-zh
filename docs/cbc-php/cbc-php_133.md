# PHP Session 和 Cookie 的区别

> 原文：[`c.biancheng.net/view/7634.html`](http://c.biancheng.net/view/7634.html)

无论是在系统运维还是 PHP 开发人员的面试中，经常会被问到 Session 和 Cookie 在 PHP 中的区别？本节我们就来归纳总结一下。

Cookie 仅由客户端生成、管理并使用，PHP 只是发出指令要求客户端如何生成 Cookie、何时过期等，但是客户端不一定会按照 PHP 的指令办事。

Cookie 不是很安全，不法分子可以通过分析本地的 Cookie 进行 Cookie 欺骗。考虑到安全问题，建议将用户的重要信息存放在 Session 中，其它不重要但需要保留的信息可以存放在 Cookie 中。

Session 是用户进入某个网站到关闭浏览器这段时间的会话，默认以文件形式存在服务器磁盘中，所以设置过多的 Session 会影响磁盘的性能，也可以用 Memory 引擎存入 MySQL，因为内存引擎读写速度快，现在也可以指定用 Redis 来处理 Session，这样更快，效率更高。

Session 的收回机制是被动的，一般来说，一旦关闭浏览器 Session 也就被 PHP 自动回收了，但有时即使设置了过期时间并且关闭浏览器也不一定会删除 Session，比如设置多目录多层级保存 Session 时，这时需要通过 PHP 脚本手动删除 Session。

通常 Cookie 与 Session 是绑定的，即用户在没有禁用 Cookie 时，Cookie 一般会保存 Session ID 及 Session 生存周期，如果用户删除 Cookie 一般会退出系统；如果没有禁用 Cookie 关闭浏览器 Session 也会立即失效，要重新登录系统。

Cookie 与 Session 一般应于标识用户、权限认证、存储简单数据、还有就是利用 Cookie 实现单点登录。

Cookie 存储的数据在不同的浏览器会有不同的限制，一般在同一个域名下，Cookie 变量数量控制在 20 个以内，每个 Cookie 的值大小控制在 4kb 以内。Session 值没有大小和数量限制，但如果数量过多，会增大服务器的压力。另外，Cookie 保存的内容是字符串，而 Session 保存的数据是对象。

Session 不能区分路径，同一个用户在访问一个网站期间，所有的 Session 在任何一个地方都可以访问到；而 Cookie 中如果设置了路径参数，那么同一个网站中不同路径下的 Cookie 是不能互相访问的。