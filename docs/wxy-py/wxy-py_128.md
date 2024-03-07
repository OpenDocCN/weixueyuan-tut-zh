# Python redis 模块的安装

> 原文：[`www.weixueyuan.net/a/763.html`](http://www.weixueyuan.net/a/763.html)

在服务器安装完毕后，需要实现自己的客户端，并通过客户端来操作服务器。构建客户端时需要使用 redis 库，该库可以使用 PIP 进行安装，方法如下：

$ sudo pip install redis
Collecting redis
  Downloading https://files.pythonhosted.org/packages/ac/a7/
cff10cc5f1180834a3ed564d148fb4329c989cbb1f2e196fc9a10fa07072/
redis-3.2.1-py2.py3-none-any.whl (65kB)
    100% |████████████████████████| 71kB 200kB/s
Installing collected packages: redis
Successfully installed redis-3.2.1

安装完成后，可以检查一下是否安装成功，启动 Python 解释器，输入下面的命令：

>>> import redis            # 引入 redis 库
>>> redis.__version__        # 得到版本信息
'3.2.1'

如果能够正确执行上面的代码并显示版本，表示客户端安装完成。下面介绍客户端基本的操作步骤。

*   引入库。
*   建立连接。
*   读写数据。

下面是一个最简单的例子：

>>> import redis                                            # 引入库
>>> r = redis.Redis(host='10.20.17.13', port=6379, db=0)    # 建立连接
>>> r.set('key1', "value1")                                    # 写入数据
True
>>> r.get('key1')                                            # 读出数据
b'value1'