# Scrapy selector 选择器用法详解

> 原文：[`www.weixueyuan.net/a/737.html`](http://www.weixueyuan.net/a/737.html)

前面介绍了如何从网页提取我们关心的数据，其提取的基本过程是首先找到页面的某个元素，然后读出该元素的值或者属性。这里的关键点是如何找到该元素，这个就是选择器的工作.

选择器从 web 页面找到我们关心的元素并提取其相关值和属性。而要想找到指定元素，首先就需要解析页面的内容，将页面数据解析成具有一定结构的各种元素的集合。

用于页面内容解析的 Python 库很多，其中最常见的是 BeautifulSoup 和 lxml：

*   BeautifulSoup 是在程序员间非常流行的网页分析库，它基于 HTML 代码的结构来构造一个 Python 对象，对不良标记的处理也非常合理，但它有一个缺点——慢。
*   lxml 是一个基于 ElementTree 的 Python 化的 XML 解析库，虽然最初是为了解析 xml 文件的，但其也可以解析 HTML。

> Scrapy 底层使用的就是 lxml。

在 Scrape 中完成解析和内容提取功能的是选择器。Scrapy 选择器的用法和 lxml 几乎是完全一样的，其支持两种格式，一种是 CSS，另外一种是 XPath。XPath 是一门用来在 XML 文件中选择节点的语言，也可以用在 HTML 上；CSS 是一门将 HTML 文档样式化的语言，选择器由它定义，并与特定的 HTML 元素的样式相关连。其中 XPath 是推荐的用法，也是我们介绍的重点。

先来看看下面的简单例子：

```

# -*- coding: utf-8 -*-
from scrapy.selector import Selector
from scrapy.http import HtmlResponse
body = '<html><body><span>good</span></body></html>'    # 输入是字符串
# 应用规则//span/text()来选择元素, 再使用 extract()来读取元素的内容
result = Selector(text=body).xpath('//span/text()').extract()
print(result)                            # 显示提取的结果
```

运行下面的命令可以查看结果：

$ python3 xpathDemo1.py
['good']

下面简单介绍一下 //span/text() 的含义，// 表示任意起始位置，如果是 / 就表示根元素了；span 表示元素的名称是 span；text() 表示提取的是内容而不是属性。由于选择条件可能找到多个元素，所以输出一般都是一个列表。

下面的例子不是从字符串，而是从网络上得到输入内容的。使用的网页地址是 http:/ /doc.scrapy.org/en/latest/_static/selectors-sample1.html (http:/%20/doc.scrapy.org/en/latest/_static/selectors-sample1.html)，其 html 内容如下：

```

<html>
<head>
  <base href='http://example.com/' />
  <title>Example website</title>
</head>
<body>
  <div id='images'>
<a href='image1.html'>Name: My image 1 <br />
<img src='image1_thumb.jpg' /></a>
   <a href='image2.html'>Name: My image 2 <br />
<img src='image2_thumb.jpg' /></a>   
   <a href='image3.html'>Name: My image 3 <br />
<img src='image3_thumb.jpg' /></a>
   <a href='image4.html'>Name: My image 4 <br />
<img src='image4_thumb.jpg' /></a>
   <a href='image5.html'>Name: My image 5 <br />
<img src='image5_thumb.jpg' /></a>
  </div>
</body>
</html>
```

现在修改代码如下，主要是通过 requests 库来得到页面内容。这次提取的是标题信息。

```

# -*- coding: utf-8 -*-
import requests                       # requests 库用来充当 HTTP 客户端
from scrapy.selector import Selector
from scrapy.http import HtmlResponse
    # 网址
url = 'http://doc.scrapy.org/en/latest/_static/selectors-sample1.html'
rsp_text = requests.get(url).text      # 得到 response 的内容   
# 构造一个 response
response = HtmlResponse(url=url, body=rsp_text, encoding='utf-8')
    # 提取 title 的内容
result = response.selector.xpath('//title/text()').extract()
print(result)                          # 显示结果
```

运行结果如下：

$ python3 xpathDemo2.py
['Example website']

## text()

在前面的例子中经常使用到 text()，其作用是得到某个元素的内容。例如上一个例子中，如果修改 Secletor 的 xpath 为 '//title'，那么提取的内容如下：

<title>Example website</title>

而如果加上 text() 就是表示仅提取该元素内容而不包含元素标签了，提取的结果如下：

Example website

下面是一个简单的例子。

```

>>> import scrapy                                  # 引入 scrapy 库
>>> html_input = "<p id='demo1'>text1</p>"     # 输入数据
    # 定义选择器
>>> sel_obj = scrapy.Selector(text=html_input, type='html')
>>> sel_seg = sel_obj.xpath("//p[@id='demo1']/text()")    # 执行提取操作
>>> type(sel_seg)                                 # 查看提取结果的类型
<class 'scrapy.selector.unified.SelectorList'>
>>> sel_seg                         # 查看提取结果的内容
[<Selector xpath="//p[@id='demo1']/text()" data=u'text1 '>]
>>> len(sel_seg)                      # 返回结果一般是列表，查看元素个数
1
>>> sel_seg[0].get()                  # 查看第一个元素的值
u'text1 '
```

## extract()/extract_first()

由于 xpath() 返回的对象类型是选择器 Selector，所以需要使用 extract() 或者 extract_first() 将其转换成字符串列表或者字符串。

extract() 返回的是一个列表，因为找到的元素可能会有很多，例如在上一个例子中，如果将 xpath 改成“//a/”我们就得到 5 个符合条件的元素，返回值就会是一个列表。

下面的例子演示了 extract() 的用法：

```

>>> import scrapy                                  # 引入 scrapy 库
>>> html_input = "<p id='demo1'>text1</p>"     # 输入数据
      # 定义选择器
>>> sel_obj = scrapy.Selector(text=html_input, type='html')
>>> sel_seg = sel_obj.xpath("//p[@id='demo1']/text()")    # 执行提取操作
>>> type(sel_seg)                                  # 查看提取结果的类型
<class 'scrapy.selector.unified.SelectorList'>
>>> sel_seg                                      # 查看提取结果的内容
[<Selector xpath="//p[@id='demo1']/text()" data=u'text1 '>]
    # 使用 extract()
>>> sel_seg2 = sel_obj.xpath("//p[@id='demo1']/text()").extract()
>>> type(sel_seg2)                                 # 查看类型
<type 'list'>
>>> sel_seg2                                     # 查看提取的内容
[u'text1']
```

extract_first() 仅返回第一个符合条件的元素，所以返回值不是一个列表。下面是其使用方法：

extract_first()

extract_first() 有一个可选的参数 default，就是如果没有找到任何符合条件的元素，那么就返回该默认值。下面的例子演示了 extract_first() 的用法：

```

>>> import scrapy                # 引入 Scrapy 库
    # 输入数据
>>> html_input = "<p id='demo1'>text1</p><p id='demo2'>text2</p>"
    # 定义选择器
>>> sel_obj = scrapy.Selector(text=html_input, type='html')
>>>                                 # 执行提取操作, 提取所有的符合条件的元素
>>> sel_seg1 = sel_obj.xpath("//p/text()").extract()
>>> sel_seg1                    # 查看提取结果
[u'text1', u'text2']
>>>                                 # 仅仅提取第一个符合条件的元素
>>> sel_seg2 = sel_obj.xpath("//p/text()").extract_first()
>>> sel_seg2                     # 查看提取结果
u'text1'
    # 提取不存在的元素 div
>>> sel_seg3 = sel_obj.xpath("//div/text()").extract_first()
>>> type(sel_seg3)                 # 返回值为 None
<type 'NoneType'>
>>>                                 # 提取不存在的元素 div，并且提供了默认值
>>> sel_seg4 = sel_obj.xpath("//div/text()").extract_first("default
     value")
>>> sel_seg4                      # 查看返回值
'default value'
```

## @属性

有时想得到的不是元素值而是其属性，例如上个例子，如果只想知道其链接的目标地址，那么便可以提取 href 属性的值，xpath 的写法就是“//a/@href”。

下面就是一个提取某个属性值的例子：

```

>>> import scrapy                  # 引入 scrapy 库
>>>                                 # 输入数据
>>> html_input = "<p id='demo1'><a href=’next’>text1</a></p><p id=
     'demo2'>text1</p>"
    # 定义选择器
>>> sel_obj = scrapy.Selector(text=html_input, type='html')
>>> sel_obj.xpath("//p/a/@href").extract_first()    # 提取 href 的属性值
u'next'
    # 另外一个例子，提取 id 的值   
>>> html_input = "<p id='demo1'>text3</p>"
    # 定义选择器
>>> sel_obj = scrapy.Selector(text=html_input, type='html')
sel_obj.xpath("//p/@id").extract_first()            # 提取 id 属性
u'demo1'
    # 提取 class 属性
>>> html_input = "<p id='demo1' class=’show collapsible’>text3</p>"
      # 定义选择器
>>> sel_obj = scrapy.Selector(text=html_input, type='html')
>>> sel_obj.xpath("//p/@class").extract_first()    # 提取 class 属性
u'show collapsible'.      # 注意，多个 class 属性并没有分开，需要自己做处理   
    # 将多个属性转换成列表的形式
>>> sel_obj.xpath("//p/@class").extract_first().split()
[u'show', u'collapsible']
```

## 元素[@属性]

如果我们要选择的是元素，但是希望对元素进行基于属性的过滤，例如 href 属性值为 image1.html 的 a 元素，那么 xpath 就可以写成“//a[@href="image2.html"]”。

下面是提取指定属性的元素的例子：

```

>>> import scrapy                    # 引入 Scrapy 库
    # 输入数据
>>> html_input = "<p id='demo1'>text1</p><p id='demo2'>text2</p>"
    # 定义选择器
>>> sel_obj = scrapy.Selector(text=html_input, type='html')
>>>                                     # 找到 id 为 demo2 的元素的文本
>>> sel_obj.xpath("//p[@id='demo1']/text()").extract_first()   
u'next'
>>>                                     # 选择带有 id 属性的元素
>>> html_input = "<p id='demo1'>text1</p><p>text2</p>"
    # 定义选择器
>>> sel_obj = scrapy.Selector(text=html_input, type='html')
>>>                                     # 选择带有 id 属性的元素的文本
>>> sel_obj.xpath("//p[@id]/text()").extract_first()
u'next'
>>>                                     # 选择没有 id 属性的元素的文本
>>> sel_obj.xpath("//p[not(@id)]/text()").extract_first()
u'demo2'
```