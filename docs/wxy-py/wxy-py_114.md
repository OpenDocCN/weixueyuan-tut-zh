# Scrapy Spider 类的用法

> 原文：[`www.weixueyuan.net/a/735.html`](http://www.weixueyuan.net/a/735.html)

Spider 类定义了如何爬取某个（或某些）网站，包括爬取的动作（例如：是否跟进链接）以及如何从网页的内容中提取结构化数据（爬取 item）。换句话说，Spider 就是定义爬取的动作及分析某个网页（或者是多个网页）的地方。

定义爬虫是我们最重要的任务，而 scrapy.Spider 是最基本的类，所有我们编写的爬虫必须继承自该类。

下面先来看一个非常简单的例子，该例子仅爬取两级页面来获得需要的数据。爬虫的种子页面是 http://stackoverflow.com/questions?sort=votes，其上显示被点赞最多的话题，每页默认显示 15 项。但是话题的详细信息并不在这个页面上，而是在其链接所指向的二级页面上。

所以为了得到我们的数据，需要进行两步操作：

1.  打开种子页面，读取出链接信息。
2.  打开被链接页面，读取需要的内容。

由于这些数据是动态的，所以读者看到的数据也许和下面显示内容的略有不同。

```

#encoding: utf-8
import scrapy                            # 引入 scrapy 模块
# 定义自己的爬虫, 必须继承自 scrapy.Spider
class StackOverflowSpider(scrapy.Spider):
    name = 'stackoverflow'               # 爬虫的名字   
    # 种子地址
    start_urls = ['http://stackoverflow.com/questions?sort=votes']
    # 回调函数 1，用来解析列表页面
    def parse(self, response):
        for href in response.css('.question-summary h3 a::attr(href)'):
            full_url = response.urljoin(href.extract())
            yield scrapy.Request(full_url, callback=self.parse_question)
    # 回调函数 2，用来解析某个有问题的页面
    def parse_question(self, response):
        yield {                           # 输出结果，字典类型
            'title': response.css('h1 a::text').extract()[0],
            'votes': response.css('.question .js-vote-count::text').
                 extract()[0],
            'body': response.css('.question .post-text').extract()[0],
            'tags': response.css('.question .post-tag::text').extract(),
            'link': response.url,
        }
```

运行命令`scrapy runspider ex2.py-o ex2.json`，可以得到输出文件 ex2.json。下面显示了该输出文件的部分内容。每个页面一条记录，每个记录包含票数 votes、地址 link、标签 tags、标题 title 和内容 body。

[
    {"votes": "4783",
     "link": "https://stackoverflow.com/questions/...",
     "tags": ["python", "namespaces", "main", "python-module", "idioms"],
     "title": "What does ..."
    },
    {"votes": "5159",
     "link": "https://stackoverflow.com/questions/...",
     "tags": ["security", "http", "authentication", "language-agnostic",
          "article"],
     "title": "The definitive guide to ..."
     },
…                        # 省略一些数据
]

那么执行命令`scrapy runspider ex2.py-o ex2.json`后，后台究竟发生了什么呢？首先 Scrapy 在文件 ex2.py 中查找 Spider 类或者派生类，找到后将其在引擎中执行。

Scrapy 首先读取定义在 start_urls 属性中的 URL（在本示例中，就是 StackOverflow 的点赞数最多的页面的 URL），创建请求，并且将接收到的 response 作为参数来调用默认的回调函数 parse()，从而启动爬取。在回调函数 parse() 中，我们使用 CSS 选择器（Selector）来提取链接。接着产生（yield）更多的请求，将函数 parse_question() 注册为这些请求完成时的回调函数。

这里可以注意到 Scrapy 的一个最主要的优势：请求是被异步调度和处理的。这意味着 Scrapy 并不需要等待一个请求完成及处理，与此同时，也可以发送其他请求或者做其他事情；也意味着当有些请求失败或者处理过程中出现错误时，其他的请求还可以继续处理。

这种工作方式允许用户可以以非常快的速度进行爬取（以容忍错误的方式同时发送多个请求），Scrapy 也通过一些设置来允许用户控制其爬取的方式。例如，可以为两个请求之间设置下载延迟、限制单域名（domain）或单个 IP 的并发请求量，甚至可以使用自动限制插件来自动处理这些问题。

最终，parse_question() 回调函数从每个页面中爬取到问题（question）的数据并产生了一个 dict，Scrapy 收集并按照终端（command line）的要求将这些结果写入到了 json 文件中。

## Spider 类的基类

我们所定义的爬虫都是派生自 Scrapy.spiders.Spider。这是一个最简单的爬虫，其仅实现了 start_requests() 的默认实现，读取并请求 spider 属性中的 start_urls，并根据返回的结果（resulting responses）调用 spider 的 parse() 方法。

下面介绍爬虫类的一些基本属性和方法。

#### 1) Name

定义爬虫的名字，类型是字符串，Scrapy 通过名字来找到对应的爬虫，所以其必须是唯一的。但是可以生成多个相同名字的爬虫实例，这没有任何限制。

名字是爬虫的最重要的属性，定义新的爬虫时一定要给其赋值。如果该爬虫只是为了爬取单个网站，一个常见的做法是以爬虫的名字包含该网站的域名信息。例如，如果某个爬虫只爬取网站 website-abc.com，那么该爬虫通常会被命名为 website-abc。

#### 2) allowed_domains

这是一个可选的属性，包含了爬虫允许爬取的域名列表。当 OffsiteMiddleware 设置启用时，域名不在列表中的 URL 不会被跟进。

#### 3) start_urls

URL 地址列表。当没有指定特定的 URL 时，爬虫将从该列表中开始进行爬取。因此，第一个被获取到的页面的 URL 将是该列表之一，后续的 URL 将会从获取到的数据中提取。

#### 4) custom_settings

该设置是一个字典。当启动爬虫时，该设置将会覆盖项目级的设置。由于设置必须在实例化之前被更新，所以该属性必须定义为类属性。

#### 5) start_requests()

该方法必须返回一个可迭代对象，该对象包含了爬虫用于爬取的第一个 Request。

当爬虫启动爬取并且未指定 URL 时，该方法被调用。当指定了 URL 时，make_requests_from_url() 将被调用来创建 Request 对象。该方法仅会被 Scrapy 调用一次，因此可以将其实现为生成器。

该方法的默认实现是使用 start_urls 的 URL 生成 Request。如果想要修改最初爬取某个网站的 Request 对象，可以重写该方法。例如，如果需要在启动时以 POST 登录某个网站，可以按照下面的方法编写代码：

```

class Ex3Spider(scrapy.Spider):          # 定义自己的爬虫类
    name = 'ex3-spider'                   # 爬虫名
    def start_requests(self):
        return [scrapy.FormRequest("http://www.ex3.com/login",
                                   formdata={'user': 'john', 'pass': 'secret'},
                                   callback=self.logged_in)]
    def logged_in(self, response):
        pass                                 # 提取页面信息，目前为空
```

#### 6) make_requests_from_url(url)

该方法接收一个 URL 地址并返回用于爬取的请求对象。该方法在初始化请求时被 start_requests() 调用，也被用于转化 URL 为请求。

默认未被重写的情况下，该方法返回的请求对象中，parse() 作为回调函数，dont_filter 参数也被设置为开启。

#### 7) parse(response)

当回应没有指定回调函数时，该方法是 Scrapy 处理下载的回应的默认方法。

parse() 函数负责处理回应消息并返回处理的数据以及跟进的 URL。Spider 对其他的请求的回调函数也有相同的要求。该方法及其他的请求回调函数必须返回一个包含 Request、字典或 Item 的可迭代的对象。

#### 8) log(message[,level,component])

使用 scrapy.log.msg() 方法记 录 message，记录中自动带上该爬虫的名字属性。

#### 9) closed(reason)

当爬虫关闭时，该函数会被调用。该方法提供了一个替代调用 signals.connect() 函数来监听 spider_closed 信号的快捷方式。