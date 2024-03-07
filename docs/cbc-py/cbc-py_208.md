# Python urllib.request 模块读取资源用法详解

在 urllib.request 子模块下包含了一个非常实用的`urllib.request.urlopen(url, data=None)` 方法，该方法用于打开 url 指定的资源，并从中读取数据。根据请求 url 的不同，该方法的返回值会发生动态改变。如果 url 是一个 HTTP 地址，那么该方法返回一个 http.client.HTTPResponse 对象。

例如如下程序：

```
from urllib.request import *

# 打开 URL 对应的资源
result = urlopen('http://www.crazyit.org/index.php')
# 按字节读取数据
data = result.read(326)
# 将字节数据恢复成字符串
print(data.decode('utf-8'))

# 用 context manager 来管理打开的 URL 资源
with urlopen('http://www.crazyit.org/index.php') as f:
    # 按字节读取数据
    data = f.read(326)
    # 将字节数据恢复成字符串
    print(data.decode('utf-8'))
```

上面程序都是向 http://www.crazyit.org/index.php 发送请求，并请求下载该页面的内容。只不过第一个示例程序是直接获取 http://www.crazyit.org/index.php 资源的数据；第二个示例程序则使用 context manager 来管理通过 urlopen 打开的资源。

运行上面程序，可以看到程序分两次获取并输出了 http://www.crazyit.org/index.php 页面内容。

在使用 urlopen() 函数时，可以通过 data 属性向被请求的 URL 发送数据。例如如下程序：

```
from urllib.request import *

# 向 https://localhost/cgi-bin/test.cgi 发送请求数据
#with urlopen(url='https://localhost/cgi-bin/test.cgi',
with urlopen(url='http://localhost:8888/test/test',    #①
    data='测试数据'.encode('utf-8')) as f:
    # 读取服务器全部响应
    print(f.read().decode('utf-8'))
```

上面程序为 data 属性指定了一个 bytes 字节数据，该字节数据会以原始二进制流的方式提交给服务器。

上面程序需要在本地（localhost）部署一个 Web 应用，该程序对应的服务器端所使用的 CGI 代码为：

#!/usr/bin/env python
import sys
data = sys.stdin.read()
print('Content-type: text/plain\n\nGot Data:"%s"' % data)

运行上面程序，可以看到如下输出结果：

Got Data:"测试数据"

如果使用 urlopen() 函数向服务器页面发送 GET 请求参数，则无须使用 data 属性，直接把请求参数附加在 URL 之后即可。例如如下代码：

```
import urllib.parse
params = urllib.parse.urlencode({'name': 'fkit', 'password': '123888'})
# 将请求参数添加到 URL 的后面
url = 'http://localhost:8888/test/get.jsp?%s' % params
with urlopen(url=url) as f:
    # 读取服务器全部响应
    print(f.read().decode('utf-8'))
```

正如程序中第 4 行代码所看到的，程序在发送 GET 请求参数时，只要将请求参数附加到 URL 的后面即可（上面程序同样需要服务器的支持）。

运行上面程序，可以看到如下输出结果：

name 参数：fkit
password 参数：123888

如果想通过 urlopen() 函数发送 POST 请求参数，则同样可通过 data 属性来实现。例如如下代码：

```
import urllib.parse
params = urllib.parse.urlencode({'name': '疯狂软件', 'password': '123888'})
params = params.encode('utf-8')
# 使用 data 指定请求参数
with urlopen("http://localhost:8888/test/post.jsp", data=params) as f:
    print(f.read().decode('utf-8'))
```

从上面第 5 行代码可以看到，如果要向指定地址发送 POST 请求，那么通过 data 指定请求参数即可。

实际上，使用 data 属性不仅可以发送 POST 请求，还可以发送 PUT、PATCH、DELETE 等请求，此时需要使用 urllib.request.Request 来构建请求参数。程序使用 urlopen() 函数打开远程资源时，第一个 url 参数既可以是 URL 字符串，也可以使用 urllib.request.Request 对象。

urllib.request.Request 对象的构造器如下：

urllib.request.Request(url, data=None, headers={}, origin_req_host=None, unverifiable=False, method=None)

从该构造器可以看出，使用 Request 可以通过 method 指定请求方法，也可以通过 data 指定请求参数，还可以通过 headers 指定请求头。

下面代码示范了如何通过 Request 对象来发送 PUT 请求：

```
from urllib.request import *

params = 'put 请求数据'.encode('utf-8')
# 创建 Request 对象，设置使用 PUT 请求
req = Request(url='http://localhost:8888/test/put',
    data=params, method='PUT')
with urlopen(req) as f:
    print(f.status)
    print(f.read().decode('utf-8'))
```

正如从代码中所看到的，程序在创建 Request 对象时通过 method 指定使用 PUT 请求方式，这意味着程序会发送 PUT 请求。

正如刚刚所提到的，程序也可以使用 Request 对象来添加请求头。例如如下代码：

```
# 创建 Request 对象
req = Request('http://localhost:8888/test/header.jsp')
# 添加请求头
req.add_header('Referer', 'http://www.crazyit.org/')
with urlopen(req) as f:
    print(f.status)
    print(f.read().decode('utf-8'))
```

程序通过 Request 的 add_header() 方法添加了一个 Referer 请求头，服务器端的处理程序将可以读到此处添加的请求头。

通过 urlopen() 函数打开远程资源之后，也可以非常方便地读取远程资源（甚至实现多线程下载）。如下程序实现了一个多线程下载的工具类：

```
from urllib.request import *
import threading

class DownUtil:
    def __init__(self, path, target_file, thread_num):
        # 定义下载资源的路径
        self.path = path
        # 定义需要使用多少个线程下载资源
        self.thread_num = thread_num
        # 指定所下载的文件的保存位置
        self.target_file = target_file
        # 初始化 threads 数组
        self.threads = []
    def download(self):
        # 创建 Request 对象
        req = Request(url=self.path, method='GET')
        # 添加请求头
        req.add_header('Accept', '*/*')
        req.add_header('Charset', 'UTF-8')
        req.add_header('Connection', 'Keep-Alive')
        # 打开要下载的资源
        f = urlopen(req)
        # 获取要下载的文件大小
        self.file_size = int(dict(f.headers).get('Content-Length', 0))
        f.close()
        # 计算每个线程要下载的资源大小
        current_part_size = self.file_size // self.thread_num + 1
        for i in range(self.thread_num):
            # 计算每个线程下载的开始位置
            start_pos = i * current_part_size
            # 每个线程使用一个 wb 模式打开的文件进行下载
            t = open(self.target_file, 'wb')
            # 定位该线程的下载位置
            t.seek(start_pos, 0);
            # 创建下载线程
            td = DownThread(self.path, start_pos, current_part_size, t)
            self.threads.append(td)
            # 启动下载线程
            td.start()
    # 获取下载的完成百分比
    def get_complete_rate(self):
        # 统计多个线程已经下载的总大小
        sum_size = 0
        for i in range(self.thread_num):
            sum_size += self.threads[i].length
        # 返回已经完成的百分比
        return sum_size / self.file_size
class DownThread(threading.Thread):
    def __init__(self, path, start_pos, current_part_size, current_part):
        super().__init__()
        self.path = path
        # 当前线程的下载位置
        self.start_pos = start_pos
        # 定义当前线程负责下载的文件大小
        self.current_part_size = current_part_size
        # 当前线程需要下载的文件块
        self.current_part = current_part
        # 定义该线程已下载的字节数
        self.length = 0
    def run(self):
        # 创建 Request 对象
        req = Request(url = self.path, method='GET')
        # 添加请求头
        req.add_header('Accept', '*/*')
        req.add_header('Charset', 'UTF-8')
        req.add_header('Connection', 'Keep-Alive')
        # 打开要下载的资源
        f = urlopen(req)
        # 跳过 self.start_pos 个字节，表明该线程只下载自己负责的那部分内容
        for i in range(self.start_pos):
            f.read(1)
        # 读取网络数据，并写入本地文件
        while self.length < self.current_part_size:
            data = f.read(1024)
            if data is None or len(data) <= 0:
                break
            self.current_part.write(data)
            # 累计该线程下载的总大小
            self.length += len(data)
        self.current_part.close()
        f.close()
```

程序中定义了 DownThread 线程类，该线程类负责读取从 start_pos 开始、长度为 current_part_size 的所有字节数据，并写入本地文件对象中。DownThread 线程类的 run() 方法就是一个简单的输入/输出实现。

程序中 DownUtils 类的 download() 方法负责按如下步骤来实现多线程下载：

1.  使用 urlopen() 方法打开远程资源。
2.  获取指定的 URL 对象所指向资源的大小（通过 Content-Length 响应头获取）。
3.  计算每个线程应该下载网络资源的哪个部分（从哪个字节开始，到哪个字节结束）。
4.  依次创建并启动多个线程来下载网络资源的指定部分。

上面程序已经实现了多线程下载的核心代码，如果要实现断点下载，则需要额外增加一个配直文件（读者可以发现，所有的断点下载工具都会在下载开始时生成两个文件：一个是与网络资源具有相同大小的空文件；一个是配置文件），该配置文件分别记录每个线程已经下载到哪个字节，当网络断开后再次开始下载时，每个线程根据配置文件中记录的位置向后下载即可。

有了上面的 DownUtil 工具类之后，接下来就可以在主程序中调用该工具类的 download() 方法执行下载，如下面的程序所示：

```
from DownUtil import *

du = DownUtil("http://www.crazyit.org/data/attachment/" \
    + "forum/201801/19/121212ituj1s9gj8g880jr.png", 'a.png', 3)
du.download()
def show_process():
    print("已完成：%.2f" % du.get_complete_rate())
    global t
    if du.get_complete_rate() < 1:
        # 通过定时器启动 0.1 之后执行 show_process 函数
        t = threading.Timer(0.1, show_process)
        t.start()
# 通过定时器启动 0.1 之后执行 show_process 函数
t = threading.Timer(0.1, show_process)
t.start()
```

运行上面程序，即可看到程序从 www.crazyit.org 下载得到一个名为 a.png 的图片文件。