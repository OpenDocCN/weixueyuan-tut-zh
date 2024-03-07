# Python 编写一个简单的爬虫

> 原文：[`www.weixueyuan.net/a/730.html`](http://www.weixueyuan.net/a/730.html)

继《Scrapy 安装（针对不同平台）》一节成功安装 Scrapy 之后，下面来看一个简单的例子。该例子仅包含一个源文件，内容如下：

```

#encoding: utf-8
import scrapy                              # 引入 Scrapy 库
class Example1(scrapy.Spider):             # 定义自己的爬虫类
    name = 'example1'                      # 爬虫的名称是 example1
    start_urls = ['https://blog.scrapinghub.com']        # 种子页面
    def parse(self, response):                           # 处理函数
        # 对于选择器选中的所有元素
        for title in response.css('.post-header>h2'):
            # 返回标题的内容
            yield {'title': title.css('a ::text').get()}
        # 对于下一个按钮
        for next_page in response.css('a.next-posts-link'):
            # 将其放入待处理列表
            yield response.follow(next_page, self.parse)
```

运行结果如下：

$ scrapy runspider ex1.py               # 参数就是我们前面的 Python 文件
# 显示一些系统信息，如 Scrapy 的版本、Python 的版本
2019-03-19 02:30:59 [scrapy.utils.log] INFO: Scrapy 1.6.0 started (bot:    scrapybot)
…                                        # 省略一些常规信息
    # 显示设置信息
2019-03-19 02:30:59 [scrapy.crawler] INFO: Overridden settings:
{'SPIDER_LOADER_WARN_ONLY': True}
…                                           # 省略一些设置信息
2019-03-19 02:30:59 [scrapy.middleware] INFO: Enabled item pipelines:
[]
    # 启动爬虫
2019-03-19 02:30:59 [scrapy.core.engine] INFO: Spider opened
…                                           # 省略一些爬虫操作信息
2019-03-19 02:31:01 [scrapy.core.engine] DEBUG: Crawled (200)
    # 爬取页面 blog.scrapinghub.com
<GET https://blog.scrapinghub.com> (referer: None)
2019-03-19 02:31:01 [scrapy.core.scraper] DEBUG: Scraped from
<200 https://blog.scrapinghub.com>
{'title': u'Spidermon: …'}                    # 显示提取到的信息
# 爬取页面 https://blog.scrapinghub.com/page/13
2019-03-19 02:31:12 [scrapy.core.engine] DEBUG: Crawled (200)
<GET https://blog.scrapinghub.com/page/13>
# 其从 https://blog.scrapinghub.com/page/12 推荐过来
(referer: https://blog.scrapinghub.com/page/12)
2019-03-19 02:31:12 [scrapy.core.scraper] DEBUG: Scraped from
<200 https://blog.scrapinghub.com/page/13>
# 得到爬取的数据，从后面的 html 第 2 行提取得到
{'title': u'Autoscraping casts a wider net'}
2019-03-19 02:31:12 [scrapy.core.scraper] DEBUG: Scraped from
<200 https://blog.scrapinghub.com/page/13>
{'title': u'Scrapy 0.14 released'}           # 显示提取到的信息
2019-03-19 02:31:12 [scrapy.core.scraper] DEBUG: Scraped from
<200 https://blog.scrapinghub.com/page/13>
{'title': u'Dirbot - a new example Scrapy project'}   #显示提取到的信息
2019-03-19 02:31:12 [scrapy.core.scraper] DEBUG: Scraped from
<200 https://blog.scrapinghub.com/page/13>
{'title': u'Introducing w3lib and scrapely'}
2019-03-19 02:31:12 [scrapy.core.scraper] DEBUG: Scraped from
<200 https://blog.scrapinghub.com/page/13>
{'title': u'Scrapy 0.12 released'}
2019-03-19 02:31:12 [scrapy.core.scraper] DEBUG: Scraped from
<200 https://blog.scrapinghub.com/page/13>
{'title': u'Spoofing your Scrapy bot IP using tsocks'}
2019-03-19 02:31:12 [scrapy.core.scraper] DEBUG: Scraped from
<200 https://blog.scrapinghub.com/page/13>
{'title': u'Hello, world'}
# 爬虫结束任务
2019-03-19 02:31:12 [scrapy.core.engine] INFO: Closing spider (finished)
# 显示统计信息
2019-03-19 02:31:12 [scrapy.statscollectors] INFO: Dumping Scrapy stats:
{'downloader/request_bytes': 4959,               # 请求消息的字节数
'downloader/request_count': 13,                # 请求的个数
'downloader/request_method_count/GET': 13,       # GET 请求的个数
'downloader/response_bytes': 134788,              # 回应消息的字节数
'downloader/response_count': 13,                  # 回应消息的个数
'downloader/response_status_count/200': 13,     # 200 回应消息的个数
'finish_reason': 'finished',                     # 状态是完成
     # 完成时间
'finish_time': datetime.datetime(2019, 3, 18, 18, 31, 12, 721064),
'item_scraped_count': 127,                     # 抓取的结果数目
'log_count/DEBUG': 140,                           # 调试消息个数
'log_count/INFO': 9,                               # INFO 消息个数
'memusage/max': 54374400,  
'memusage/startup': 54366208,
'request_depth_max': 12,                       # 最大深度为 12
'response_received_count': 13,              # 接收到的回应消息个数
'scheduler/dequeued': 13,
'scheduler/dequeued/memory': 13,
'scheduler/enqueued': 13,
'scheduler/enqueued/memory': 13,
'start_time': datetime.datetime(2019, 3, 18, 18, 30, 59, 636948)}
2019-03-19 02:31:12 [scrapy.core.engine] INFO: Spider closed (finished)

运行命令`scrapy runspider ex1.py`来启动爬虫，输出的内容非常多，现阶段我们仅需要了解其中带有注释的一部分内容。

下面是从网页上爬取到的原始数据，爬虫就是处理这样的数据并将我们关心的信息提取出来。# 开头的内容是我们添加的描述信息。

```

<div class="post-header">
  # 标题，我们提取的就是这些标题信息
  <h2><a href="https://blog.scrapinghub.com/xxx ">Scrapy 0.14 released
       </a></h2>
  <div class="byline">                         # 正文框
    <span class="date">                       # 日期
      <i class="fa fa-calendar-o"></i>
      <a href="https://blog.scrapinghub.com/xxx">November 18, 2011 </a>
    </span>
    <span class="author">                      # 作者信息
      <i class="fa fa-user"></i>
        <a href="https://blog.scrapinghub.com/xxx">Pablo Hoffman</a>
    </span>
    <span class="custom_listing_comments">     # 评论
      <i class="fa fa-comments-o"></i>
      <a href="https://blog.scrapinghub.com/xxx">0 Comment</a>
    </span>
  </div> 
</div>
```

后面章节会继续详细讲述如何提取 html 内容，这里暂时不详述，仅需要知道前面代码的 14～15 行完成了 html 内容的提取。

for title in response.css('.post-header>h2'):
    yield {'title': title.css('a ::text').get()}