# Scrapy 配置项用法详解

> 原文：[`www.weixueyuan.net/a/741.html`](http://www.weixueyuan.net/a/741.html)

Scrapy 提供了定制 Scrapy 组件的方法，用户可以定制核心（core）、插件（extension）、pipeline 及 spider 组件。

如果使用某个配置文件，就需要设定环境变量 SCRAPY_SETTINGS_MODULE，其格式是以“.”连接起来的，而不是用“/”连接起来的。而且该模块必须能够在 Python 的搜索路径中。

Scrapy 提供了很多种配置的方法，不同的方法有不同的优先级和作用范围。下面列出了这些配置的方法，高优先级的放在前面：

*   命令行选项（最高优先级）。
*   每个 spider 的设定。
*   项目设定模块。
*   命令默认设定模块。
*   全局默认设定（最低优先级）。

## 1、命令行方式

命令行传入的参数具有最高的优先级。用户可以使用命令行选项 -s（或 --set）来覆盖一个（或更多）选项。下面的例子设置了 LOG_FILE，值为 scrapy.log。

$ crapy crawl spider1 -s LOG_FILE=scrapy.log

类似的用法，可以设置 USER-AGENT，这样可以伪装成其他浏览器的样子。

下面是定义 USER-AGENT 的一个例子：

```

# 在命令行中定义 USER_AGENT
$ scrapy crawl spider1 -s USER_AGENT=Mozilla/Firefox
2019-03-28 02:27:47 [scrapy.utils.log] INFO: Scrapy 1.6.0 started (bot:
     ex1)
# 其他版本信息
2019-03-28 02:27:47 [scrapy.crawler] INFO: Overridden settings:
{'NEWSPIDER_MODULE': 'ex1.spiders',
'SPIDER_MODULES': ['ex1.spiders'], 'ROBOTSTXT_OBEY': True,
# 我们设定的值
'USER_AGENT': 'Mozilla/Firefox', 'BOT_NAME': 'ex1'}   
… # 其他信息
```

多数情况下，我们希望同时设定多个选项，这时就可以使用多个 -s 命令行参数，例如下面的例子便同时设置了 LOG_FILE 和 USER_AGENT：

$ crapy crawl spider1-s LOG_FILE=scrapy.log -s USER_AGENT=Mozilla/Firefox

## 2、爬虫范围内的设置

这个设置的优先级高于项目级别的设置。方式是修改爬虫类的属性 custom_settings。该属性是一个字典，键就是属性的名，值就是属性的值。

下面是一个例子，其定义了某个爬虫的配置项：

```

# -*- coding: utf-8 -*-
import scrapy
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule
class DemoSpider(scrapy.Spider):
    name = 'demo'
    # 定义相关设置
    custom_settings = {
       'CONCURRENT_ITEMS': 88,                 # 设定同时处理的结果个数
       'CONCURRENT_REQUESTS': 66              # 设置同时处理的请求个数
    }
    def parse_item(self, response):           # 数据提取函数
        pass
```

## 3、项目范围内的设置

其通过项目中的 setting.py 文件来设置。其格式是定义变量，例如：

AUTOTHROTTLE_DEBUG = False

下面是在创建完项目后就存在的默认设置，多数是被 # 注释掉的。

```

# -*- coding: utf-8 -*-
# 一些项目相关的描述信息
# Scrapy settings for ex1 project
…                                        # 省略一些配置信息
BOT_NAME = 'ex1'                           # 项目
SPIDER_MODULES = ['ex1.spiders']          # 爬虫信息
ROBOTSTXT_OBEY = True                      # 是否遵循 robots 协议
# 同时发出的请求数目，这里被注释掉了就表示使用默认值
#CONCURRENT_REQUESTS = 32
#DOWNLOAD_DELAY = 3                      # 下载之间的间隔时间
# 对于某个域名可以同时发起的请求数目
#CONCURRENT_REQUESTS_PER_DOMAIN = 16
# 对于同一个 IP 地址可以同时发起的请求数目
#CONCURRENT_REQUESTS_PER_IP = 16   
#COOKIES_ENABLED = False                   # 是否使用 cookie
# 默认头部，可以在这里定义我们关心的值
#DEFAULT_REQUEST_HEADERS = {
#   'Accept': 'text/html,application/xhtml+xml,application/xml;q=
     0.9,*/*;q=0.8',
#   'Accept-Language': 'en',
#}
```

这里预定义的设置只是常用的，我们可以按照需要自行添加新的配置项，由于这个文件就是一个 Python 文件，格式遵循 Python 格式即可。这里的配置对整个项目有效。

## 4、命令默认设定

就是 scrapy.cfg 文件，其采用 ini 格式，和普通的配置文件用法相同。其会在下面的位置查找该文件。

*   系统级别：/etc/scrapy.cfg 或 c:\scrapy\scrapy.cfg。
*   用户级别：~/.config/scrapy.cfg($XDG_CONFIG_HOME)及~/.scrapy.cfg($HOME)。
*   项目级别：在 Scrapy 项目根路径下的 scrapy.cfg。

如果查到多个配置文件，Scrapy 会进行合并操作。如果对于同一个属性在多个文件中都有定义，那么项目级别的优先级最高，系统级别的优先级最低，此时仅高优先级的定义才有效。

## 5、全局默认设定

全局默认设定存储在 scrapy.settings.default_settings 模块。下面演示了如何查看这些配置项。

>>> import scrapy.settings
>>> dir(scrapy.settings.default_settings)
['AJAXCRAWL_ENABLED', 'AUTOTHROTTLE_DEBUG',
'AUTOTHROTTLE_ENABLED',
'AUTOTHROTTLE_MAX_DELAY', 'AUTOTHROTTLE_START_DELAY',
'AUTOTHROTTLE_TARGET_CONCURRENCY', 'BOT_NAME',
'CLOSESPIDER_ERRORCOUNT', 'CLOSESPIDER_ITEMCOUNT',
'CLOSESPIDER_PAGECOUNT', 'CLOSESPIDER_TIMEOUT',
'COMMANDS_MODULE', 'COMPRESSION_ENABLED', 'CONCURRENT_ITEMS',
'CONCURRENT_REQUESTS', 'CONCURRENT_REQUESTS_PER_DOMAIN',
'CONCURRENT_REQUESTS_PER_IP', 'COOKIES_DEBUG', 'COOKIES_ENABLED',
'DEFAULT_ITEM_CLASS', 'DEFAULT_REQUEST_HEADERS',
'DEPTH_LIMIT', 'DEPTH_PRIORITY', 'DEPTH_STATS_VERBOSE',
'DNSCACHE_ENABLED', 'DNSCACHE_SIZE', 'DNS_TIMEOUT',
'DOWNLOADER', 'DOWNLOADER_CLIENTCONTEXTFACTORY',
'DOWNLOADER_CLIENT_TLS_METHOD',
'DOWNLOADER_HTTPCLIENTFACTORY',
'DOWNLOADER_MIDDLEWARES', 'DOWNLOADER_MIDDLEWARES_BASE',
'DOWNLOADER_STATS', 'DOWNLOAD_DELAY',
'DOWNLOAD_FAIL_ON_DATALOSS',
'DOWNLOAD_HANDLERS', 'DOWNLOAD_HANDLERS_BASE',
'DOWNLOAD_MAXSIZE', 'DOWNLOAD_TIMEOUT',
'DOWNLOAD_WARNSIZE', 'DUPEFILTER_CLASS',
'EDITOR', 'EXTENSIONS', 'EXTENSIONS_BASE', 'FEED_EXPORTERS',
'FEED_EXPORTERS_BASE', 'FEED_EXPORT_ENCODING',
'FEED_EXPORT_FIELDS', 'FEED_EXPORT_INDENT', 'FEED_FORMAT',
'FEED_STORAGES', 'FEED_STORAGES_BASE', 'FEED_STORE_EMPTY',
'FEED_TEMPDIR', 'FEED_URI', 'FEED_URI_PARAMS',
'FILES_STORE_GCS_ACL', 'FILES_STORE_S3_ACL',
'FTP_PASSIVE_MODE', 'FTP_PASSWORD', 'FTP_USER',
'HTTPCACHE_ALWAYS_STORE', 'HTTPCACHE_DBM_MODULE',
'HTTPCACHE_DIR', 'HTTPCACHE_ENABLED',
'HTTPCACHE_EXPIRATION_SECS', 'HTTPCACHE_GZIP',
'HTTPCACHE_IGNORE_HTTP_CODES', 'HTTPCACHE_IGNORE_MISSING',
'HTTPCACHE_IGNORE_RESPONSE_CACHE_CONTROLS',
'HTTPCACHE_IGNORE_SCHEMES', 'HTTPCACHE_POLICY',
'HTTPCACHE_STORAGE', 'HTTPPROXY_AUTH_ENCODING',
'HTTPPROXY_ENABLED', 'IMAGES_STORE_GCS_ACL',
'IMAGES_STORE_S3_ACL', 'ITEM_PIPELINES', 'ITEM_PIPELINES_BASE',
'ITEM_PROCESSOR', 'LOGSTATS_INTERVAL', 'LOG_DATEFORMAT',
'LOG_ENABLED', 'LOG_ENCODING', 'LOG_FILE', 'LOG_FORMAT',
'LOG_FORMATTER', 'LOG_LEVEL', 'LOG_SHORT_NAMES', 'LOG_STDOUT',
'MAIL_FROM', 'MAIL_HOST', 'MAIL_PASS', 'MAIL_PORT',
'MAIL_USER', 'MEMDEBUG_ENABLED', 'MEMDEBUG_NOTIFY',
'MEMUSAGE_CHECK_INTERVAL_SECONDS', 'MEMUSAGE_ENABLED',
'MEMUSAGE_LIMIT_MB', 'MEMUSAGE_NOTIFY_MAIL',
'MEMUSAGE_WARNING_MB', 'METAREFRESH_ENABLED',
'METAREFRESH_MAXDELAY', 'NEWSPIDER_MODULE',
'RANDOMIZE_DOWNLOAD_DELAY', 'REACTOR_THREADPOOL_MAXSIZE',
'REDIRECT_ENABLED', 'REDIRECT_MAX_TIMES',
'REDIRECT_PRIORITY_ADJUST', 'REFERER_ENABLED',
'REFERRER_POLICY', 'RETRY_ENABLED',
'RETRY_HTTP_CODES', 'RETRY_PRIORITY_ADJUST', 'RETRY_TIMES',
'ROBOTSTXT_OBEY', 'SCHEDULER', 'SCHEDULER_DEBUG',
'SCHEDULER_DISK_QUEUE', 'SCHEDULER_MEMORY_QUEUE',
'SCHEDULER_PRIORITY_QUEUE', 'SPIDER_CONTRACTS',
'SPIDER_CONTRACTS_BASE', 'SPIDER_LOADER_CLASS',
'SPIDER_LOADER_WARN_ONLY', 'SPIDER_MIDDLEWARES',
'SPIDER_MIDDLEWARES_BASE', 'SPIDER_MODULES', 'STATSMAILER_RCPTS',
'STATS_CLASS', 'STATS_DUMP', 'TELNETCONSOLE_ENABLED',
  'TELNETCONSOLE_HOST', 'TELNETCONSOLE_PASSWORD',
'TELNETCONSOLE_PORT', 'TELNETCONSOLE_USERNAME',
'TEMPLATES_DIR', 'URLLENGTH_LIMIT', 'USER_AGENT',
'__builtins__', '__doc__', '__file__', '__name__',
'__package__', 'abspath', 'dirname', 'import_module',
'join', 'six', 'sys']

可以看到这些配置项基本上都是全大写的。

## 6、读取配置

一般通过爬虫对象的 settings 属性来获得。这个属性是一个字典，键就是配置项的名字，值就是配置项的值。

下面的代码就是显示所有配置项的名称：

```

# -*- coding: utf-8 -*-
import scrapy
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule
class settingUsageDemoSpider(scrapy.Spider):      # 定义爬虫
    name = 'settingUsageDemoSpider'              # 爬虫名字
    start_urls = ['http://example.com']           # 种子页面
     def parse(self, response):                  # 处理函数
        # 打印所有的配置项的名称
        print("Existing settings: %s" % self.settings.attributes.
             keys())
```

下面是运行时的结果：

$ scrapy crawl settingUsageDemoSpider           # 启动爬虫
...                                                 # 省略一些常规输出
# 显示所有配置项的名称
Existing settings: ['CLOSESPIDER_ITEMCOUNT', 'RETRY_HTTP_CODES',
'MAIL_FROM', 'HTTPCACHE_EXPIRATION_SECS', 'SPIDER_LOADER_CLASS',
'FTP_PASSIVE_MODE', 'COMPRESSION_ENABLED', 'DOWNLOAD_TIMEOUT',
'DNSCACHE_SIZE', 'MEMUSAGE_LIMIT_MB', 'EXTENSIONS', 'DEPTH_PRIORITY',
'FEED_TEMPDIR', 'TELNETCONSOLE_HOST', 'MEMDEBUG_NOTIFY', 'FTP_USER',
'REFERRER_POLICY', 'DOWNLOAD_WARNSIZE', 'SPIDER_MODULES',
'RETRY_TIMES', 'IMAGES_STORE_GCS_ACL', 'TELNETCONSOLE_PORT',
'TELNETCONSOLE_ENABLED', 'DOWNLOADER_MIDDLEWARES',
'HTTPCACHE_DBM_MODULE', 'ROBOTSTXT_OBEY', 'DEPTH_LIMIT',
'REACTOR_THREADPOOL_MAXSIZE', 'FEED_EXPORT_FIELDS',
'SCHEDULER_DEBUG', 'CLOSESPIDER_PAGECOUNT', 'LOG_SHORT_NAMES',
'AUTOTHROTTLE_MAX_DELAY', 'DOWNLOADER_CLIENT_TLS_METHOD',
'URLLENGTH_LIMIT', 'FEED_EXPORTERS', 'DOWNLOAD_FAIL_ON_DATALOSS',
'LOG_ENCODING', 'FEED_EXPORTERS_BASE', 'FEED_FORMAT',
'DOWNLOAD_DELAY', 'HTTPCACHE_GZIP',
'DOWNLOADER_MIDDLEWARES_BASE', 'DNSCACHE_ENABLED',
'CONCURRENT_REQUESTS_PER_IP', 'DOWNLOADER_HTTPCLIENTFACTORY',
'EDITOR', 'MAIL_HOST', 'IMAGES_STORE_S3_ACL', 'CONCURRENT_REQUESTS',
'AUTOTHROTTLE_START_DELAY', 'CLOSESPIDER_ERRORCOUNT',
'STATS_CLASS', 'FEED_STORAGES_BASE', 'ITEM_PROCESSOR',
'AUTOTHROTTLE_ENABLED', 'COMMANDS_MODULE', 'AUTOTHROTTLE_DEBUG',
'NEWSPIDER_MODULE', 'FILES_STORE_S3_ACL', 'DEPTH_STATS_VERBOSE',
'CONCURRENT_ITEMS', 'DOWNLOAD_MAXSIZE', 'FTP_PASSWORD', 'MAIL_PORT',
'REFERER_ENABLED', 'HTTPCACHE_POLICY', 'STATS_DUMP',
'MEMUSAGE_NOTIFY_MAIL', 'DOWNLOAD_HANDLERS', 'LOG_DATEFORMAT',
'HTTPCACHE_ENABLED', 'DOWNLOADER_STATS', 'FEED_EXPORT_ENCODING',
'REDIRECT_ENABLED', 'REDIRECT_MAX_TIMES', 'REDIRECT_PRIORITY_ADJUST',
'DUPEFILTER_CLASS', 'SPIDER_LOADER_WARN_ONLY', 'RETRY_ENABLED',
'SPIDER_CONTRACTS', 'MAIL_PASS', 'LOG_ENABLED', 'MAIL_USER',
'HTTPCACHE_ALWAYS_STORE', 'LOGSTATS_INTERVAL',
'SCHEDULER_PRIORITY_QUEUE', 'DNS_TIMEOUT',
'DOWNLOADER_CLIENTCONTEXTFACTORY',
'MEMUSAGE_CHECK_INTERVAL_SECONDS', 'EXTENSIONS_BASE',
'FEED_STORAGES', 'BOT_NAME', 'DEFAULT_ITEM_CLASS',
'SPIDER_CONTRACTS_BASE', 'METAREFRESH_MAXDELAY',
'CONCURRENT_REQUESTS_PER_DOMAIN',
'HTTPCACHE_IGNORE_HTTP_CODES', 'TELNETCONSOLE_PASSWORD',
'MEMUSAGE_WARNING_MB', 'FEED_STORE_EMPTY', 'COOKIES_DEBUG',
'HTTPPROXY_AUTH_ENCODING', 'FEED_URI', 'SPIDER_MIDDLEWARES',
'DOWNLOADER', 'AUTOTHROTTLE_TARGET_CONCURRENCY', 'USER_AGENT',
'AJAXCRAWL_ENABLED', 'COOKIES_ENABLED', 'LOG_FORMAT',
'TELNETCONSOLE_USERNAME', 'FEED_EXPORT_INDENT', 'ITEM_PIPELINES',
'LOG_LEVEL', 'HTTPCACHE_IGNORE_RESPONSE_CACHE_CONTROLS',
'METAREFRESH_ENABLED', 'HTTPCACHE_IGNORE_MISSING',
'HTTPPROXY_ENABLED', 'HTTPCACHE_IGNORE_SCHEMES',
'SCHEDULER_MEMORY_QUEUE', 'SCHEDULER_DISK_QUEUE',
'RANDOMIZE_DOWNLOAD_DELAY', 'SETTINGS_MODULE', 'TEMPLATES_DIR',
'FILES_STORE_GCS_ACL', 'LOG_STDOUT', 'LOG_FORMATTER', 'LOG_FILE',
'HTTPCACHE_STORAGE', 'MEMDEBUG_ENABLED', 'FEED_URI_PARAMS',
'DEFAULT_REQUEST_HEADERS', 'CLOSESPIDER_TIMEOUT', 'SCHEDULER',
'SPIDER_MIDDLEWARES_BASE', 'ITEM_PIPELINES_BASE',
'STATSMAILER_RCPTS', 'MEMUSAGE_ENABLED']
... # 省略其他的普通输出，包括常规统计输出
2019-03-20 02:49:55 [scrapy.core.engine] INFO: Spider closed (finished)

## 7、常用配置

本节介绍一些常用的配置项，这些配置项会影响爬虫的爬取行为。

#### 1) BOT_NAME

bot 的名称，默认值是 scrapybot。

#### 2) CONCURRENT_ITEMS

同时处理 item 的个数，默认值是 100。

#### 3) CONCURRENT_REQUESTS

同时发出的请求的个数，默认值是 16。

#### 4) CONCURRENT_REQUESTS_PER_DOMAIN

对于单个网站进行并发请求的个数，默认值是 8。

#### 5) CONCURRENT_REQUESTS_PER_IP

对于某个 IP 地址，可以同时发送的请求个数，0 表示没有限制。默认值是 0；如果非 0，则忽略配置项 CONCURRENT_REQUESTS_PER_DOMAIN。

#### 6) DEFAULT_REQUEST_HEADERS

请求中的 header 内容，默认值如下：

{
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
    'Accept-Language': 'en',
}

#### 7) DEPTH_LIMIT

爬虫的深度限制，如果为 0 表示没有深度限制。默认值是 0。

#### 8) DNS_TIMEOUT

处理 DNS 的时长限制，单位秒，可以是浮点数。默认值是 60。

#### 9) DOWNLOAD_DELAY

下载器在下载同一个网站下一个页面前需要等待的时间。该配置项可以用来限制爬取速度，减轻服务器压力。单位是秒，可以为浮点数，如 0.25 表示 250 毫秒。默认值是 0。

#### 10) RANDOMIZE_DOWNLOAD_DELAY

有些网站会检测一些请求的相似性，例如请求间隔非常规律，将阻止该类型的后续请求。为了绕开这个问题，可以设置 RANDOMIZE_DOWNLOAD_DELAY=True，这样请求之间的间隔就是 DOWNLOAD_DELAY*[0.5,1.5]，就是说请求间隔是一个 0.5 到 1.5 之间的随机数乘以 DOWNLOAD_DELAY，这样就比原来固定的间隔更有迷惑性。如果 DOWNLOAD_DELAY=0，RANDOMIZE_DOWNLOAD_DELAY 就没有效果了。

#### 11) DOWNLOAD_TIMEOUT

下载器超时时间，单位是秒。默认值是 180。

#### 12) DOWNLOAD_MAXSIZE

下载器下载的内容大小限制，单位是字节。默认值是 1073741824，表示 1024MB。如果不想有这个限制，设置其为 0。

#### 13) LOG_ENABLED

是否启动日志功能，默认值是 True，表示启动日志功能。

#### 14) LOG_FILE

日志文件名，None 表示将日志写入到标准输出。默认值是 None。

#### 15) LOG_LEVEL

设定日志的级别，默认值是 DEBUG。可选的值包括 CRITICAL、ERROR、WARNING、INFO 和 DEBUG。

#### 16) ROBOTSTXT_OBEY

是否启用 robot 服务。如果启用，Scrapy 将会尊重 robots.txt 策略。默认值是 False，就是不考虑 obots.txt 策略。

#### 17) STATS_DUMP

当 spider 结束时显示 Scrapy 状态数据。默认值是 True，就是会显示状态数据。