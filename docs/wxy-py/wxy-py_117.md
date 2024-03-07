# Scrapy 命令行用法精讲

> 原文：[`www.weixueyuan.net/a/742.html`](http://www.weixueyuan.net/a/742.html)

前面的例子中已经演示了一些用法，如创建项目、创建爬虫等，本节将做一个比较系统的介绍。

Scrapy 命令其实就是一个 Python 脚本，其内容如下所示：

```

# -*- coding: utf-8 -*-
import re                           # 正则表达式
import sys                      # 用来得到命令行参数信息
from scrapy.cmdline import execute
if __name__ == '__main__':
    sys.argv[0] = re.sub(r'(-script\.pyw?|\.exe)?$', '', sys.argv[0])
    sys.exit(execute())
```

## 1、创建项目

比较常见的用法是创建一个项目，该项目包含有配置文件、爬虫文件以及结果数据的定义等。也可以使用 Scrapy 命令来创建一个项目的基本框架，方法如下：

scrapy startproject 项目名称 [目录名称]

其中目录名称是可选的，如果没有指定，目录名称就是项目名称。下面是两个简单的例子，演示了两个常用的用法。

$ scrapy startproject ex1  #ex1 是项目的名称
New Scrapy project 'ex1', using template directory
'/usr/local/lib/python2.7/site-packages/scrapy/templates/project',    created in:
/Users/py/work/scrapy/ex1
You can start your first spider with:
    cd ex1
       scrapy genspider example example.com [u'text1', u'text2']
# 指定项目目录名称，如果没有指定就是项目的名称
$ scrapy startproject ex2 ex2Folder
New Scrapy project 'ex2', using template directory
'/usr/local/lib/python2.7/site-packages/scrapy/templates/project',
     created in:
/Users/py/work/scrapy/ex2Folder
You can start your first spider with:
    cd ex2Folder
    scrapy genspider example example.com

## 2、创建爬虫

在创建项目后，需要创建爬虫。默认情况下是不会自动创建爬虫的，所以这一步是必须要做的。可以使用下面命令格式来创建一个爬虫：

scrapy genspider [选项参数] 爬虫名 域名

常见的选项参数如表 1 所示。

表 1 创建爬虫命令的主要参数

| 参 数 | 说 明 |
| --force | 强制创建爬虫，即使该爬虫已经存在了 |
| -t 模板名 --template=模板名 | 指定模板，如果没有指定，就使用 basic 模板 |

下面的例子演示了常见的爬虫创建方法：

# 使用 basic 模板来创建爬虫
$ scrapy genspider spider1 domain1.com
Created spider 'spider1' using template 'basic' in module:
    ex1.spiders.spider1
# 查看这个爬虫的内容
$ cat ex1/spiders/spider1.py
# -*- coding: utf-8 -*-
import scrapy
   class Spider1Spider(scrapy.Spider):
    name = 'spider1'
    allowed_domains = ['domain1.com']
    start_urls = ['http://domain1.com/']
     def parse(self, response):
         pass
# 如果再次创建同名爬虫会出现错误
$ scrapy genspider spider1 domain2.com
Spider 'spider1' already exists in module:
    ex1.spiders.spider1
# 如果带上-force，那么就可以覆盖原来的内容
$ scrapy genspider --force spider1 domain2.com
Created spider 'spider1' using template 'basic' in module:
    ex1.spiders.spider1
# 指定模版，前面没有指定模版，所以使用 basic 模版
$ scrapy genspider -t crawl spider2 domain3.com
Created spider 'spider2' using template 'crawl' in module:
    ex1.spiders.spider2

## 3、列出爬虫模板

我们也通过 Scrapy 命令行来查看当前系统存在哪些爬虫模板，下面是该命令的格式：

scrapy genspider -l

下面例子演示了如何查看当前系统存在的爬虫模板。

# 列出所有的爬虫模板
$ scrapy genspider -l
Available templates:
  basic
  crawl
  csvfeed
  xmlfeed

在 Ubuntu Linux 下，安装完 Scrapy 之后就存在目录 /usr/local/lib/python3.5/dist-packages/scrap y/templates/spiders，在该目录下可以看到这些爬虫模板文件。

# 列出模板文件
$ ls -l /usr/local/lib/python3.5/dist-packages/scrapy/templates/spiders
total 16
-rw-r--r-- 1 root staff 208 Mar 10 17:52 basic.tmpl
-rw-r--r-- 1 root staff 657 Mar 10 17:52 crawl.tmpl
-rw-r--r-- 1 root staff 571 Mar 10 17:52 csvfeed.tmpl
-rw-r--r-- 1 root staff 565 Mar 10 17:52 xmlfeed.tmpl

下面是 basic 模板的内容：

# 查看 basic 模板文件的内容
$ cat /usr/local/lib/python3.5/dist-packages/scrapy/templates/spiders/ basic.tmpl
# -*- coding: utf-8 -*-
import scrapy
class $classname(scrapy.Spider):           # 自定义的爬虫
    name = '$name'
    allowed_domains = ['$domain']
    start_urls = ['http://$domain/']
    def parse(self, response):
        pass

## 4、查看某个爬虫模板

在前面章节中，我们通过 cat 命令来查看某个模板的内容。Scrape 也提供了相关的命令来查看模板的内容，其优点是不需要记住模板的位置。

这个查看模板内容的命令格式如下：

scrapy genspider -d 模板名称

下面的例子演示了如何查看 basic 模板内容：

# 查看 basic 模板文件的内容
$ scrapy genspider -d basic
# -*- coding: utf-8 -*-
import scrapy   
class $classname(scrapy.Spider):
    name = '$name'
    allowed_domains = ['$domain']
    start_urls = ['http://$domain/']
    def parse(self, response):
        pass

> 这里需要注意的是，$name 和 $domain 是变量，在使用模板生成具体爬虫时会用实际的值来代替。

下面查看另外一个模板 crawl 的内容：

# 查看 crawl 模板文件的内容
$ scrapy genspider -d crawl
# -*- coding: utf-8 -*-
import scrapy
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule   
class $classname(CrawlSpider):
    name = '$name'
    allowed_domains = ['$domain']
    start_urls = ['http://$domain/']
    rules = (
        Rule(LinkExtractor(allow=r'Items/'), callback='parse_item',
             follow=True),
    )
    def parse_item(self, response):
        item = {}
        #item['domain_id'] = response.xpath('//input[@id="sid"]/@value').
              get()
        #item['name'] = response.xpath('//div[@id="name"]').get()
        #item['description'] = response.xpath('//div[@id="description"]').
             get()
        return item

## 5、基于模板创建爬虫

可以使用模板创建自己的爬虫，这样会比从零开始创建一个完整的爬虫要节省一些时间。如果对从模板创建出来的爬虫不是很满意，也可以手动修改生成出来的爬虫。

Scrapy 自带了 basic、crawl、csvfeed 和 xmlfeed 4 种模板，我们可以使用命令来创建基于它们的爬虫，命令格式如下：

scrapy genspider -t 模板名 爬虫名 域名

这里的爬虫名会替代模板中的变量 $name，而域名会替代模板中的变量 $domain。

下面基于 crawl 模板来创建我们的爬虫 templateSpider，域名为 stackoverflow.com：

# 基于模板 crawl 创建爬虫 templateSpider
# $domain=stackoverflow.com
$ scrapy genspider -t crawl templateSpider stackoverflow.com
Created spider 'templateSpider' using template 'crawl' in module:
  ex1.spiders.templateSpider
$ scrapy list                                      # 查看该爬虫
templateSpider.                                   # 这就是我们新建的爬虫
$ cat ex1/spiders/templateSpider.py             # 查看文件
# -*- coding: utf-8 -*-
…                                                  # 省略 6 行常规代码
class TemplatespiderSpider(CrawlSpider):
    name = 'templateSpider'
    allowed_domains = ['stackoverflow.com']
    start_urls = ['http://stackoverflow.com/']
    …                                             # 省略 11 行常规代码

## 6、启动爬虫

在定义完爬虫后可以通过命令行来将其启动，命令格式如下：

scrapy crawl 爬虫名

下面是某个爬虫启动的过程：

$ scrapy crawl spider1                          # 启动爬虫 spider1
# 显示一些系统版本信息
2019-03-28 02:51:54 [scrapy.utils.log] INFO: Scrapy 1.6.0 started (bot:
     ex1)
2019-03-28 02:51:54 [scrapy.utils.log] INFO: Versions: lxml 4.3.2.0,
libxml2 2.9.9, cssselect 1.0.3, parsel 1.5.1, w3lib 1.20.0, Twisted
     18.9.0,
Python 2.7.15 (default, Oct  2 2018, 11:47:18) –
[GCC 4.2.1 Compatible Apple LLVM 10.0.0 (clang-1000.11.45.2)],
pyOpenSSL 19.0.0 (OpenSSL 1.1.0j  20 Nov 2018), cryptography 2.4.2,
Platform Darwin-17.7.0-x86_64-i386-64bit
2019-03-28 02:51:54 [scrapy.crawler] INFO:        # 爬虫信息
Overridden settings: {'NEWSPIDER_MODULE': 'ex1.spiders', '
SPIDER_MODULES': ['ex1.spiders'], 'ROBOTSTXT_OBEY': True, 'BOT_NAME':    'ex1'}
# 扩展信息
2019-03-28 02:51:54 [scrapy.middleware] INFO: Enabled extensions:
['scrapy.extensions.memusage.MemoryUsage',
…                                              # 省略扩展信息
      # 爬虫爬取内容的信息
2019-03-28 02:51:56 [scrapy.downloadermiddlewares.retry]
DEBUG: Retrying <GET http://domain2.com/>
…                                              # 省略详细爬取过程信息
# 统计信息
2019-03-28 02:51:56 [scrapy.statscollectors] INFO: Dumping Scrapy stats:
…                                             # 省略详细统计信息
# 关闭爬虫
2019-03-28 02:51:56 [scrapy.core.engine] INFO: Spider closed (finished)

## 7、爬虫列表

可以通过爬虫列表命令来查看当前项目下全部的爬虫，命令格式如下：

scrapy list

下面的例子演示了爬虫列表命令的用法：

$ scrapy list                      # 显示当前所有的爬虫
settingUsageDemoSpider
spider1
spider2
templateSpider
# 创建一个新的爬虫 new_spider_1
$ scrapy genspider new_spider_1 stackoverflow.com
Created spider 'new_spider_1' using template 'basic' in module:
  ex1.spiders.new_spider_1
$ scrapy list                      # 再次查看当前所有的爬虫
new_spider_1                        # 这就是刚才新建的爬虫
settingUsageDemoSpider
spider1
spider2
templateSpider

需要注意的是，必须在项目目录下面使用该命令，否则命令会失败。

$ scrapy list                   # 不在项目目录下，显示没有活动的项目
Scrapy 1.6.0 - no active project
Unknown command: list
Use "scrapy" to see available commands

## 8、爬取指定页面

爬取指定命令可以直接指定一个页面地址，让爬虫使用当前配置或者系统配置来爬取内容，这样便可以查看页面内容，或者 http 的头部信息。该命令格式如下：

scrapy fetch 页面地址

页面内容是在标准输出显示的。

下面的例子演示了仅输出 html 和仅输出 header 信息的用法。

# 得到 http://www.example.com/l 的页面信息
$ scrapy fetch http://www.example.com/
…                                        # 省略一些系统信息内容
2019-03-21 08:42:01 [scrapy.middleware] INFO: Enabled item pipelines:
[]    # pipelines
2019-03-21 08:42:01 [scrapy.core.engine] INFO: Spider opened
2019-03-21 08:42:01 [scrapy.extensions.logstats] INFO:
Crawled 0 pages (at 0 pages/min), scraped 0 items (at 0 items/min)
2019-03-21 08:42:01 [scrapy.extensions.telnet] INFO:
Telnet console listening on 127.0.0.1:6024
2019-03-21 08:42:02 [scrapy.core.engine] DEBUG:
Crawled (404) <GET http://www.example.com/robots.txt> (referer: None)
2019-03-21 08:42:02 [scrapy.core.engine] DEBUG: Crawled (404)
<GET http://www.example.com/> (referer: None)
<!doctype html>                         # 页面内容
…                                           # 省略页面内容
2019-03-21 08:42:02 [scrapy.core.engine] INFO: Closing spider (finished)
2019-03-21 08:42:02 [scrapy.statscollectors] INFO: Dumping Scrapy
     stats:
…                                        # 省略统计信息
2019-03-21 08:42:02 [scrapy.core.engine] INFO: Spider closed (finished)
# 仅显示页面内容
$ scrapy fetch --nolog http://www.example.com/
<!doctype html>
<html>
…                                           # 省略页面内容
</html>
# 仅显示 http 的头部信息
$ scrapy fetch --nolog --headers http://www.example.com/
> Accept-Language: en
> Accept-Encoding: gzip,deflate
> Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;
     q=0.8
> User-Agent: Scrapy/1.6.0 (+https://scrapy.org)
>
< X-Cache: HIT
< Accept-Ranges: bytes
< Expires: Thu, 28 Mar 2019 00:52:14 GMT
< Vary: Accept-Encoding
< Server: ECS (oxr/830A)
< Last-Modified: Fri, 09 Aug 2013 23:54:35 GMT
< Etag: "1541025663"
< Cache-Control: max-age=604800
< Date: Thu, 21 Mar 2019 00:52:14 GMT
< Content-Type: text/html; charset=UTF-8
# 我们希望指定 USER-AGENT，不用默认的 Scrapy/1.6.0 (+https://scrapy.org)
$ scrapy fetch –nolog --headers \
    --set=USER_AGENT='MyUA' http://www.example.com/
> Accept-Language: en
> User-Agent: MyUA               # 这就是我们设置的 USER-AGENT
…                                 # 省略其他的输出

## 9、在浏览器中显示页面

如果希望查看某个页面的内容，可以使用 scrapy view 这个命令。该命令会启动系统默认的浏览器来打开指定的页面，并且使用的是 Scrapy 项目的配置信息。该命令的格式如下：

scrapy view 页面地址

由于使用的是项目的配置，所有其显示的内容可能和直接在浏览器中输入的网页地址不同。可以使用 scrapy view 命令来查看不同，并且可以在浏览器中检视内容，或者做一些调试操作。

下面的例子演示了该命令的用法：

# 启动 view 命令来打开页面 http://stackoverflow.com/questions?sort=votes
$ scrapy view http://stackoverflow.com/questions?sort=votes
2019-03-21 08:28:54 [scrapy.utils.log] INFO: Scrapy 1.6.0 started (bot:
     ex1)
…                            # 省略一些系统信息内容
        5    2019-03-21 08:28:54 [scrapy.middleware] INFO: Enabled extensions:
…                            # 省略一些 middlewares 的输出
2019-03-21 08:28:54 [scrapy.middleware] INFO: Enabled item pipelines:
[]
2019-03-21 08:28:54 [scrapy.core.engine] INFO: Spider opened
2019-03-21 08:28:54 [scrapy.extensions.logstats] INFO:
Crawled 0 pages (at 0 pages/min), scraped 0 items (at 0 items/min)
2019-03-21 08:28:54 [scrapy.extensions.telnet] INFO:
Telnet console listening on 127.0.0.1:6024
2019-03-21 08:28:55 [scrapy.downloadermiddlewares.redirect] DEBUG:
Redirecting (301) to <GET https://stackoverflow.com/robots.txt>
from <GET http://stackoverflow.com/robots.txt>
2019-03-21 08:28:56 [scrapy.core.engine] DEBUG: Crawled (200)
       # 得到爬虫协议文件
<GET https://stackoverflow.com/robots.txt> (referer: None)。
2019-03-21 08:28:56 [scrapy.downloadermiddlewares.redirect] DEBUG:
Redirecting (301) to <GET https://stackoverflow.com/questions?sort=
     votes> from
<GET http://stackoverflow.com/questions?sort=votes>
    2019-03-21 08:28:57 [scrapy.core.engine] DEBUG: Crawled (200)
<GET https://stackoverflow.com/questions?sort=votes> (referer: None)
2019-03-21 08:28:57 [scrapy.core.engine] INFO: Closing spider
     (finished)
2019-03-21 08:28:57 [scrapy.statscollectors] INFO: Dumping Scrapy
     stats:
…                                         # 省略统计信息
2019-03-21 08:28:57 [scrapy.core.engine] INFO: Spider closed (finished)

其将页面下载到本地，然后启动浏览器来查看。注意看浏览器中的地址栏，显示的不是 stackoverflow.com，而是一个以 file:// 开头的本地文件。

## 10、启动交互式 shell

前面的用法都是批量执行一个脚本，我们也可以使用交互的方式来执行。该方式一般是用来测试提取数据的代码，也就是用来测试 XPath 或 CSS 表达式，查看它们的工作方式及从爬取的网页中提取的数据。

在编写 spider 时，该终端提供了交互性测试表达式代码的功能，免去了每次修改后运行完整 spider 的麻烦。一旦熟悉了 Scrapy 终端后，就会发现其在开发和调试爬虫时的巨大作用。如果安装了 IPython，Scrapy 终端将使用。如果没有安装 IPython，那么其启动标准的 CPython 终端。

该命令的格式如下：

scrapy shell [页面地址]

其会启动一个 shell，我们可以以交互的方式在 shell 内进行操作。下面的例子中，我们在 ex1 项目下打开页面 http://www.example.com/，其会自动使用当前设置去下载该页面的内容，并且将 request、response 等信息都以变量的形式提供给我们。

# 启动 shell，并让其打开 http://www.example.com/
$ scrapy shell http://www.example.com/
2019-03-20 08:52:42 [scrapy.utils.log] INFO: Scrapy 1.6.0 started (bot:
     ex1)
…                                                 # 省略 scrapy 的常规输出
2019-03-20 08:52:43 [scrapy.core.engine] DEBUG: Crawled (200)
<GET http://www.example.com/> (referer: None)     # 下载页面
[s] Available Scrapy objects:
[s]   scrapy     scrapy module (contains scrapy.Request, scrapy.Selector,
     etc)
[s]   crawler    <scrapy.crawler.Crawler object at 0x108a67c90>
[s]   item       {}
[s]   request    <GET http://www.example.com/>
[s]   response   <200 http://www.example.com/>
[s]   settings   <scrapy.settings.Settings object at 0x108a67f10>
[s]   spider     <DefaultSpider 'default' at 0x108eb7450>
[s] Useful shortcuts:
[s]   fetch(url[, redirect=True]) Fetch URL and update local objects
(by default, redirects are followed)
[s]   fetch(req)                  Fetch a scrapy.Request and update local
     objects
[s]   shelp()           Shell help (print this help)
[s]   view(response)    View response in a browser

这时就可以查看各种信息了，如 settings、request、response 等，方法如下：

>>> request                       # 查看请求信息
<GET http://www.example.com/>
>>> request.method               # 查看请求的方法
'GET'
>>> request.url                  # 查看请求的地址
'http://www.example.com/'
>>> response                      # 查看回应消息
<200 http://www.example.com/>
>>> response.status              # 查看状态码
200

也可以使用 xpath 来提取相关内容，方法如下：

>>> response.xpath("//h1/text()").extract()    # 查看所有 h1 标签的内容
[u'Example Domain']
>>> response.xpath("//a/@href").extract()     # 提取页面中包含的链接的地址
[u'http://www.iana.org/domains/example']   
>>> response.xpath("//head/meta").extract()    # 提取头部的 meta 标签的值
[u'<meta charset="utf-8">',
u'<meta http-equiv="Content-type" content="text/html; charset=utf-8">',
u'<meta name="viewport" content="width=device-width, initial-scale=1">']

## 11、runspider

在不创建项目时，可以使用这种方法来运行定义在指定文件中的爬虫。该命令的格式如下：

scrapy runspider 源文件

下面是一个简单的例子：

# 启动文件 spider1.py 中定义的爬虫
$ scrapy runspider spider1.py
# 显示一些系统版本信息
2019-03-28 02:51:54 [scrapy.utils.log] INFO: Scrapy 1.6.0 started (bot:
     ex1)
2019-03-28 02:51:54 [scrapy.utils.log] INFO: Versions: lxml 4.3.2.0,
libxml2 2.9.9, cssselect 1.0.3, parsel 1.5.1, w3lib 1.20.0, Twisted
     18.9.0,
Python 2.7.15 (default, Oct  2 2018, 11:47:18) –
[GCC 4.2.1 Compatible Apple LLVM 10.0.0 (clang-1000.11.45.2)],
pyOpenSSL 19.0.0 (OpenSSL 1.1.0j  20 Nov 2018), cryptography 2.4.2,
Platform Darwin-17.7.0-x86_64-i386-64bit
# 爬虫信息
2019-03-28 02:51:54 [scrapy.crawler] INFO:
Overridden settings: {'NEWSPIDER_MODULE': 'ex1.spiders', '
SPIDER_MODULES': ['ex1.spiders'], 'ROBOTSTXT_OBEY': True, 'BOT_NAME':
     'ex1'}
# 扩展信息
2019-03-28 02:51:54 [scrapy.middleware] INFO: Enabled extensions:
['scrapy.extensions.memusage.MemoryUsage',
…   # 省略扩展信息
# 爬虫爬取内容的信息
2019-03-28 02:51:56 [scrapy.downloadermiddlewares.retry]
DEBUG: Retrying <GET http://domain2.com/>
…  # 省略爬取过程
# 统计信息
2019-03-28 02:51:56 [scrapy.statscollectors] INFO: Dumping Scrapy
     stats:
…  # 省略统计数据
# 关闭爬虫
2019-03-28 02:51:56 [scrapy.core.engine] INFO: Spider closed (finished)

## 12、标定 Scrapy 的性能

Scrapy 提供了一个简单的性能测试工具，其创建了一个本地 HTTP 服务器，并以最大可能的速度进行爬取。该性能测试工具的目的是测试 Scrapy 在本地硬件上的效率，来获得一个基本的参考用于对比。其使用了一个简单的 spider，仅跟进链接，不做任何处理。

该命令格式如下：

scrapy bench

> 该命令与项目无关，不用在项目文件夹下执行。

下面的例子演示了该命令的使用情况：

# 启动标定程序
$ scrapy bench
…                                 # 一些系统信息
2019-03-21 08:11:16 [scrapy.statscollectors] INFO: Dumping Scrapy
     stats:
{'downloader/request_bytes': 266851,
'downloader/request_count': 860,
'downloader/request_method_count/GET': 860,
'downloader/response_bytes': 1275062,
'downloader/response_count': 860,
'downloader/response_status_count/200': 860,
…                                  # 其他统计信息
'start_time': datetime.datetime(2019, 3, 21, 0, 11, 6, 90223)}
2019-03-21 08:11:16 [scrapy.core.engine] INFO: Spider closed
     (closespider_timeout)

在上面的例子中可以看到其性能大致是 860 个页面/分钟。

> 注意，这是一个非常简单、仅跟进链接的 spider，任何其他 spider 都会做更多处理，那么其爬取的速度就会减慢。减慢的程度取决于 spider 做的处理以及其是如何被编写的。

## 13、其他命令

也可以用命令来查看 Scrapy 的配置信息，命令格式如下：

scrapy settings [参数]

> 该命令也不依赖于项目。

下面的例子获得了某个配置的值。

# 在项目外面，获得爬虫的名称
$ scrapy settings --get=BOT_NAME
scrapybot  # 这个是系统的默认信息
# 进入到某个项目的目录下
$ cd ex1
$ scrapy settings --get=BOT_NAME
ex1      # 这个就是从项目得到的配置信息

如果对当前使用的 Scrapy 版本不确定，也可以通过命令来获得相关的信息。其有两种用法，一种是仅获得 Scrapy 的版本信息，命令格式如下：

scrapy version

另外一种可以获得除了 Scrapy 版本信息之外的其他一些信息，如 Python 的版本信息、其使用的 lxml 库的版本信息等，该命令格式如下：

scrapy version -v

下面的例子演示了上面两种用法。

$ scrapy version # 仅显示 Scrapy 的版本信息
Scrapy 1.6.0
$ scrapy version -v  # 显示更多的信息，如 Python、lxml 等的版本信息
Scrapy       : 1.6.0
lxml         : 4.3.2.0
libxml2      : 2.9.9
cssselect    : 1.0.3
parsel       : 1.5.1
w3lib        : 1.20.0
Twisted      : 18.9.0
Python       : 2.7.15 (default, Oct  2 2018, 11:47:18) –
[GCC 4.2.1 Compatible Apple LLVM 10.0.0 (clang-1000.11.45.2)]
pyOpenSSL    : 19.0.0 (OpenSSL 1.1.0j  20 Nov 2018)
cryptography : 2.4.2
Platform     : Darwin-17.7.0-x86_64-i386-64bit