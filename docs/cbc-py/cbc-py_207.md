# Python urllib.parse 模块用法详解

URL（Uniform Resource Locator）对象代表统一资源定位器，它是指向互联网“资源”的指针。资源可以是简单的文件或目录，也可以是对复杂对象的引用，例如对数据库或搜索引擎的查询。在通常情况下，URL 可以由协议名、主机、端口和资源路径组成，即满足如下格式：

protocol://host:port/path

例如如下的 URL 地址：

http://www.crazyit.org/index.php

urllib 模块则包含了多个用于处理 URL 的子模块：

*   urllib.request：这是最核心的子模块，它包含了打开和读取 URL 的各种函数。
*   urllib.error：主要包含由 urllib.request 子模块所引发的各种异常。
*   urllib.parse：用于解析 URL。
*   urllib.robotparser：主要用于解析 robots.txt 文件。

通过使用 urllib 模块可以打开任意 URL 所指向的资源，就像打开本地文件一样，这样程序就能完整地下载远程页面。如果再与 re 模块结合使用，那么程序完全可以提取页面中各种信息，这就是所谓的“网络爬虫”的初步原理。

在 Python2.x 中，urllib 模块被分为 urllib 和 urllib2 两个模块，其中 urllib 主要用于简单的下载，而 urllib2 则可实现 HTTP 验证，cookie 管理。

下面先介绍 urllib.parse 子模块中用于解析 URL 地址和查询字符串的函数：

*   urllib.parse.urlparse(urlstring, scheme='', allow_fragments=True)：该函数用于解析 URL 字符串。程序返回一个 ParseResult 对象，可以获取解析出来的数据。
*   urllib.parse.urlunparse(parts)：该函数是上一个函数的反向操作，用于将解析结果反向拼接成 URL 地址。
*   urllib.parse.parse_qs(qs, keep_blank_values=False, strict_parsing=False, encoding='utf-8', errors='replace')：该该函数用于解析查询字符串（application/x-www-form-urlencoded 类型的数据），并以 dict 形式返回解析结果。
*   urllib.parse.parse_qsl(qs, keep_blank_values=False, strict_parsing=False, encoding='utf-8', errors='replace')：该函数用于解析查询字符串（application/x-www-form-urlencoded 类型的数据），并以列表形式返回解析结果。
*   urllib.parse.urlencode(query, doseq=False, safe='', encoding=None, errors=None, quote_via=quote_plus)：将字典形式或列表形式的请求参数恢复成请求字符串。该函数相当于 parse_qs()、parse_qsl() 的逆函数。
*   urllib.parse.urljoin(base, url, allow_fragments=True)：该函数用于将一个 base_URL 和另一个资源 URL 连接成代表绝对地址的 URL。

例如，如下程序使用 urlparse() 函数来解析 URL 字符串：

```
from urllib.parse import *

# 解析 URL 字符串
result = urlparse('http://www.crazyit.org:80/index.php;yeeku?name=fkit#frag')
print(result)
# 通过属性名和索引来获取 URL 的各部分
print('scheme:', result.scheme, result[0])
print('主机和端口:', result.netloc, result[1])
print('主机:', result.hostname)
print('端口:', result.port)
print('资源路径:', result.path, result[2])
print('参数:', result.params, result[3])
print('查询字符串:', result.query, result[4])
print('fragment:', result.fragment, result[5])
print(result.geturl())
```

上面程序中使用 urlparse() 函数解析 URL 字符串，解析结果是一个 ParseResult 对象，该对象实际上是 tuple 的子类。因此，程序既可通过属性名来获取 URL 的各部分，也可通过索引来获取 URL 的各部分。

表 1 显示了 ParseResult 各属性与元组索引的对应关系。

表 1 ParseResult 各属性与元组索引的对应关系

| 属性名 | 元组索引 | 返回值 | 默认值 |
| scheme | 0 | 返回 URL 的 scheme | scheme 参数 |
| netloc | 1 | 网络位置部分（主机名＋端口） | 空字符串 |
| path | 2 | 资源路径 | 空字符串 |
| params | 3 | 资源路径的附加参数 | 空字符串 |
| query | 4 | 查询字符串 | 空字符串 |
| fragment  | 5 | Fragment 标识符 | 空字符串 |
| username |   | 用户名 | None |
| password  |   | 密码 | None |
| hostname |   | 主机名 | None |
| port |   | 端口 | None |

上面程序使用 urlparse() 函数解析 URL 字符串之后，分别使用了属性名和索引来获取 URL 的各部分。运行上面程序，将看到如下输出结果：

ParseResult(scheme='http', netloc='www.crazyit.org:80', path='/index.php', params='yeeku', query='name=fkit', fragment='frag')
scheme: http http
主机和端口: www.crazyit.org:80 www.crazyit.org:80
主机: www.crazyit.org
端口: 80
资源路径: /index.php /index.php
参数: yeeku yeeku
查询字符串: name=fkit name=fkit
fragment: frag frag
http://www.crazyit.org:80/index.php;yeeku?name=fkit#frag

如果使用 urlunparse() 函数，则可以把一个 ParseResult 对象或元组恢复成 URL 字符串。例如如下代码：

```
result = urlunparse(('http', 'www.crazyit.org:80', 'index.php',
    'yeeku', 'name=fkit', 'frag'))
print('URL 为:', result)
```

运行上面程序，将看到如下输出结果：

URL 为: http://www.crazyit.org:80/index.php;yeeku?name=fkit#frag

如果被解析的 URL 以双斜线（//）开头，那么 urlparse() 函数可以识别出主机，只是缺少 scheme 部分。但如果被解析的 URL 既没有 scheme，也没有以双斜线（//）开头，那么 urlparse() 函数将会把这些 URL 都当成资源路径。例如如下代码：

```
# 解析以//开头的 URL
result = urlparse('//www.crazyit.org:80/index.php')
print('scheme:', result.scheme, result[0])
print('主机和端口:', result.netloc, result[1])
print('资源路径:', result.path, result[2])
print('-----------------')
# 解析没有 scheme，也没有以双斜线（//）开头的 URL
# 从开头部分开始就会被当成资源路径
result = urlparse('www.crazyit.org/index.php')
print('scheme:', result.scheme, result[0])
print('主机和端口:', result.netloc, result[1])
print('资源路径:', result.path, result[2])
```

运行上面程序， 可以看到如下输出结果：

scheme: 
主机和端口: www.crazyit.org:80 www.crazyit.org:80
资源路径: /index.php /index.php
-----------------
scheme: 
主机和端口: 
资源路径: www.crazyit.org/index.php www.crazyit.org/index.php

parse_qs() 和 parse_qsl()（这个 l 代表 list）两个函数都用于解析查询字符串，只不过返回值不同而已，parse_qsl() 函数的返回值是 list（正如该函数名所暗示的）。urlencode() 则是它们的逆函数。例如如下代码：

```
# 解析查询字符串，返回 dict
result = parse_qs('name=fkit&name=%E7%96%AF%E7%8B%82java&age=12')
print(result)
# 解析查询字符串，返回 list
result = parse_qsl('name=fkit&name=%E7%96%AF%E7%8B%82java&age=12')
print(result)
# 将列表格式的请求参数恢复成请求参数字符串
print(urlencode(result))
```

运行上面程序，将看到如下输出结果：

{'name': ['fkit', '疯狂 java'], 'age': ['12']}
[('name', 'fkit'), ('name', '疯狂 java'), ('age', '12')]
name=fkit&name=%E7%96%AF%E7%8B%82java&age=12

从上面的输出结果可以看到，parse_qs() 函数返回了一个 dict，其中 key 是参数名，value 是参数值。而 parse_qsl() 函数返回了一个 list，每个元素代表一个查向参数。

urljoin() 函数负责将两个 URL 拼接在一起，返回代表绝对地址的 URL。这里主要可能出现 3 种情况：

1.  被拼接的 URL 只是一个相对路径 path（不以斜线开头），那么该 URL 将会被拼接到 base 之后，如果 base 本身包含 path 部分，则用被拼接的 URL 替换 base 所包含的 path 部分。
2.  被拼接的 URL 是一个根路径 path（以单斜线开头），那么该 URL 将会被拼接到 base 的域名之后。
3.  被拼接的 URL 是一个绝对路径 path（以双斜线开头），那么该 URL 将会被拼接到 base 的 scheme 之后。

例如， 如下代码示范了 urljoin() 函数的功能和用法：

```
# 被拼接 URL 不以斜线开头
result = urljoin('http://www.crazyit.org/users/login.html', 'help.html')
print(result) # http://www.crazyit.org/users/help.html
result = urljoin('http://www.crazyit.org/users/login.html', 'book/list.html')
print(result) # http://www.crazyit.org/users/book/list.html
# 被拼接 URL 以斜线（代表根路径 path）开头
result = urljoin('http://www.crazyit.org/users/login.html', '/help.html')
print(result) # http://www.crazyit.org/help.html
# 被拼接 URL 以双斜线（代表绝对 URL）开头
result = urljoin('http://www.crazyit.org/users/login.html', '//help.html')
print(result) # http://help.html
```

上面代码己经给出了程序运行结果，通过运行结果可以看到 urljoin() 函数的功能。