# Python http.cookiejar 模块：管理 cookie

通过前面章节的学习，使用 urlopen() 既可发送 GET 请求，也可发送 POST、PUT、DELETE、PATCH 等请求。因此，在绝大部分时候，完全可以使用 urllib.request 模块代替 http.client 模块。

有时候，用户可能需要访问 Web 应用中的被保护页面，如果使用浏览器则十分简单，通过系统提供的登录页面登录系统，浏览器会负责维护与服务器之间的 session，如果用户登录的用户名、密码符合要求，就可以访问被保护资源了。

如果使用 urllib.request 模块来访问被保护页面，则同样需要维护与服务器之间的 session，此时就需要借助于 cookie 管理器来实现。

HTTP 是一种“请求-响应”式协议：客户端向服务器发送请求，服务器向客户揣生成响应数据。这就涉及一个问题，服务器如何辨别两次请求的客户端是同一个客户端呢？答案是 session id。当客户端第一次向服务器发送请求时，服务器会为该客户端分配一个 session id 作为其标识，服务器在生成响应数据时，也会把该 session id 作为响应数据发送给客户端。当客户端第二次向服务器发送请求时，如果客户端把自己的 session id 也发送给服务器，且服务器端的 session id 还未过期，服务器就知道该客户端与前一次发送请求的客户端是同一个。

如果程序直接使用 urlopen() 发送请求，并且并未管理与服务器之间的 session，那么服务器就无法识别两次请求是否是同一个客户端发出的。为了有效地管理 session，程序可引入 http.cookiejar 模块。

此外，程序还需要使用 OpenerDirector 对象来发送请求。为了使用 urllib.request 模块通过 cookie 来管理 session，可按如下步骤进行操作：

1.  创建 http.cookiejar.CookieJar 对象或其子类的对象。
2.  以 CookieJar 对象为参数，创建 urllib.request.HTTPCookieProcessor 对象，该对象负责调用 CookieJar 来管理 cookie。
3.  以 HTTPCookieProcessor 对象为参数，调用 urllib.request.build_opener() 函数创建 OpenerDirector 对象。
4.  使用 OpenerDirector 对象来发送请求，该对象将会通过 HTTPCookieProcessor 调用 CookieJar 来管理 cookie。

下面程序示范了先登录 Web 应用，然后访问 Web 应用中的被保护页面：

```
from urllib.request import *
import http.cookiejar, urllib.parse

# 以指定文件创建 CookieJar 对象，对象将可以把 cookie 保存在文件中
cookie_jar = http.cookiejar.MozillaCookieJar('a.txt')
# 创建 HTTPCookieProcessor 对象
cookie_processor = HTTPCookieProcessor(cookie_jar)
# 创建 OpenerDirector 对象
opener = build_opener(cookie_processor)

# 定义模拟 Chrome 浏览器的 user_agent
user_agent = r'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36' \
    r' (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36'
# 定义请求头
headers = {'User-Agent':user_agent, 'Connection':'keep-alive'}

#-------------下面代码发送登录的 POST 请求----------------
# 定义登录系统的请求参数
params = {'name':'crazyit.org', 'pass':'leegang'}
postdata = urllib.parse.urlencode(params).encode()
# 创建向登录页面发送 POST 请求的 Request
request = Request('http://localhost:8888/test/login.jsp',
    data = postdata, headers = headers)
# 使用 OpenerDirector 发送 POST 请求
response = opener.open(request)
print(response.read().decode('utf-8'))

# 将 cookie 信息写入磁盘文件
cookie_jar.save(ignore_discard=True, ignore_expires=True)  # ①

#-------------下面代码发送访问被保护资源的 GET 请求----------------
# 创建向"受保护页面"发送 GET 请求的 Request
request = Request('http://localhost:8888/test/secret.jsp',
    headers=headers)
response = opener.open(request)
print(response.read().decode())
```

上面程序中，第 5 行代码先创建了一个 CookieJar 对象，此处使用它的子类 MozillaCookieJar，该对象负责把 cookie 信息保存在文件中。第 7 行代码负责创建 HTTPCookieProcessor 对象；第 9 行代码调用 build_opener() 函数创建 OpenerDirector 对象。接下来程序就会通过该对象来发送请求，而底层的 CookieJar 对象就负责处理 cookie。

程序发送 POST 请求和 GET 请求的代码与前面章节中的示例代码并没有太大的区别，只不过此处额外设置了一个 User-Agent 请求头，该请求头用于模拟 Chrome 浏览器。

正如从上面代码所看到的，该程序同样需要设置并启动 Tomcat 服务器。运行上面程序，可以看到如下输出结果：

```
恭喜您，登录成功！
<!DOCTYPE html>
<html>
<head>
    <meta name = "author" content = "Yeeku.H.Lee(Crazyit.org)"/>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <title>安全资源</title>
</head>
<body>
    安全资源，只有登录用户<br/>
    且用户名是 crazyit.org 才可访问该资源
</body>
</html>
```

上面第一行输出信息就是发送 POST 登录请求的响应数据，运行响应数据提示“登录成功”。后面的输出信息则显示该程序成功访问了“安全资源”（这份资源要求用户必须登录才能访问），如果没有登录，直接访问是看不到该输出信息的。

如果将上面程序中 ① 号代码的注释取消，程序就会把 cookie 信息写入 a.txt 文件中。这意味着该程序将会把服务器响应的 session id 等 cookie 持久化保存在 a.txt 文件中，后面程序可以读取该 cookie 文件信息，这样程序就可以模拟前面登录过的客户端，从而直接访问被保护页面了。

例如如下程序：

```
from urllib.request import *
import http.cookiejar, urllib.parse

# 以指定文件创建 CookieJar 对象，对象将可以把 cookie 保存在文件中
cookie_jar = http.cookiejar.MozillaCookieJar('a.txt')
# 直接加载 a.txt 中的 Cookie 信息
cookie_jar.load('a.txt',ignore_discard=True,ignore_expires=True)
# 遍历 a.txt 中保存的 cookie 信息
for item in cookie_jar:
    print('Name ='+ item.name)
    print('Value ='+ item.value)
# 创建 HTTPCookieProcessor 对象
cookie_processor = HTTPCookieProcessor(cookie_jar)
# 创建 OpenerDirector 对象
opener = build_opener(cookie_processor)

# 定义模拟 Chrome 浏览器的 user_agent
user_agent = r'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36' \
    r' (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36'
# 定义请求头
headers = {'User-Agent':user_agent, 'Connection':'keep-alive'}

#-------------下面代码发送访问被保护资源的 GET 请求----------------
# 创建向"受保护页面"发送 GET 请求的 Request
request = Request('http://localhost:8888/test/secret.jsp',
    headers=headers)
response = opener.open(request)
print(response.read().decode())
```

该程序的前面部分同样用于创建 CookieJar、HTTPCookieProcessor、OpenerDirector 对象，但它多了第 7 行代码，运行代码用于从 a.txt 文件中读取 cookie 信息。接下来的第 9~11 行代码只是用于显示 a.txt 文件中所保存的 cookie 信息，这些代码不影响程序本身。

该程序并未向服务器发送登录请求，但由于该 CookieJar 会把登录成功的 session id 发送给服务器，因此服务器就会认为该程序与前面那个登录成功的程序是同一个客户端。运行上面程序，也可以访问到 Web 应用中的被保护页面，输出结果如下：

```
Name =JSESSIONID
Value =CA772F2EB65808A43A09A452005C2F55

<!DOCTYPE html>
<html>
<head>
    <meta name="author" content="Yeeku.H.Lee(Crazyit.org)"/>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <title>安全资源</title>
</head>
<body>
    安全资源，只有登录用户<br/>
    且用户名是 crazyit.org 才可访问该资源
</body>
</html>
```

上面前两行输出内容就是 a.txt 文件中保存的 cookie 信息。从该输出结果可以看到，该 cookie 信息只是保存了服务器发送给客户端的 session id。因此，程序使用 OpenerDirector 向服务器发送请求时，就会将该 session id 发送给服务器，这样服务器就会把程序当成前一个己经登录成功的程序。所以，该程序也可以访问到被保护页面。