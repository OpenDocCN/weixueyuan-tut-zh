# PHP 简介，PHP 是什么？

> 原文：[`c.biancheng.net/view/6106.html`](http://c.biancheng.net/view/6106.html)

PHP 全称 PHP Hypertext Preprocessor，是“超文本预处理器”的意思。它是一种跨平台的、开源的、免费的脚本语言，其语法吸收了 C 语言、Java 和 Perl 的特点，利于学习，使用广泛，主要适用于 Web 开发领域。

PHP 语言是最受欢迎的 Web 开发语言之一。它以学习简单、开发快速、性能稳定而备受 Web 开发人员的青睐。不仅使用人数众多，而且有强大的社区支持，使得无论是用 PHP 开发 Web 应用，还是学习 PHP 语言，都非常迅速。

## PHP 的发展历史

1994 年，拉斯姆斯·勒多夫（Rasmus Lerdorf）创造了 PHP，这个时候的 PHP 只是用 Perl 语言编写的一系列 CGI 脚本，用于跟踪他在线简历的访问情况，统计他自己网站的访问者。

勒多夫把这些 CGI 脚本命名为“Personal Home Page Tools”。这里我们姑且将之称为 PHP1 吧！这个早期的 PHP Tools 并不是一门语言，只是一些工具，提供基本的变量，并使用嵌入式 HTML 句法自动处理表单变量。

![PHP 之父 Rasmus Lerdorf](img/b6a8842040bc09e69eccf5b31d8dd3a5.png)
图：PHP 之父 Rasmus Lerdorf

#### PHP2

由于勒多夫写的这个小程序轻巧且简便，吸引了很多人的关注，1995 年，勒多夫发布了 PHP/FI 2.0 版本，该版本不仅可以做到访问数据库，还能嵌入到 HTML 中动态处理数据。

新版本的 PHP 吸引来了很多的程序开发者，其中包括 Zeev Suraski（泽埃夫·苏拉斯基）和 Andi Gutmans（安迪·古曼兹），他们后来加入到了 PHP3 的开发当中。

#### PHP3

在 1997 年中旬，开始了 PHP 第三版的开发计划， Zeev Suraski 和 Andi Gutmans 加入到了开发小组中。1998 年末，PHP3 的第一个官方正式版发行，其特点是具有更好的执行效果和更清晰的结构。此外，该版本最强大的地方在于它的可扩展性，这一特点吸引了大量的开发人员加入并提交了一系列新的 PHP 扩展模块。

新版本的 PHP 有了一个全新的名字“PHP: Hypertext Preprocessor”，这标志着 PHP 不再仅仅是个人网页的小工具。

#### PHP4

在 PHP3 发布后不久，Zeev Suraski 和 Andi Gutmans 就开始投入到 PHP4 的开发当中，主要目标是增强程序运行性能和 PHP 自身的模块性。

新的 PHP4 采用“Zend”引擎作为核心，相同的 PHP 脚本在新版本中运行比旧版本性能提升了近十倍。

在 2000 年，PHP4 正式发布，主要增加了以下特性：

*   各种 web 服务器的支持；
*   丰富的数组操作函数；
*   完整的会话机制；
*   对输出缓存的支持；
*   增加了对类和对象的支持，这也是 PHP 支持面向对象的雏形。

#### PHP5

尽管 PHP 发展势头迅猛，但相比较于 Java 语言来说，还有很多问题，比如面向对象的支持不够完善，无法捕获异常（Exception）等，这导致多年以来 PHP 一直被人们认为是一门面向过程的语言。

2004 年 7 月，PHP5 正式发布，这标志着 PHP 进入到了一个全新的时代。并且往后多年，PHP 一直在 5 这个版本上迭代，这也是 PHP 历时最长的一个大版本。

PHP5 的核心采用的是第二代 Zend 引擎，并引入了对 PECL 模块的支持，最重要的是 PHP5 全面支持了面向对象机制。

由于 PHP5 小版本众多，下表中列出了各版本的发行年份与加入的新特性：

| PHP 版本 | 发行年份 | 新特性 |
| 5.0 | 2004-07-13 | — |
| 5.1 | 2005-11-24 | — |
| 5.2 | 2006-11-02 | — |
| 5.3 | 2009-06-30 | 引入了命名空间、闭包和匿名函数 |
| 5.4 | 2012-03-01 | 引入性状（trait）、内置 web 服务器 |
| 5.5 | 2013-06-20 | 引入 PHP 生成器（generator）、MySQL 扩展被废弃，可使用 MySQLi 或者 PDO |
| 5.6 | 2014-08-28 | — |

#### PHP7

2015 年 12 月 3 日，PHP7 问世了，这是 PHP 的一次质的飞跃。PHP7 不仅修复了大量 BUG ，还新增了一些功能和语法糖。这些变化涉及到了核心包、GD 库、PDO、ZIP、ZLIB 等熟悉和不熟悉的核心功能与扩展包。

PHP7 移除了已经被废弃的函数，如 mysql_ 系列函数（在 PHP5.5 被废弃，在 PHP7 被删除），同时 PHP7 的性能是 PHP5.6 的两倍。

## PHP 的发展趋势

PHP 从诞生到现在已经有 20 多年的历史了，从 Web 时代兴起到移动互联网退潮，互联网领域各种编程语言和技术层出不穷，Node.js、Golang、Python 等不断地在挑战 PHP 的地位。这些技术的推动者非常热衷于唱衰 PHP，PHP 语言的未来在哪里？PHP 程序员当如何应对未来的变革？

作为老牌的 Web 后端编程语言，PHP 在全球市场占有率是非常高的，仅次于 Java ，从各个招聘网站的数据上来看 PHP 开发的职位非常多，薪资水平也非常不错。

实际上在中小企业、互联网创业公司中，PHP 的市场地位是高于 Java 的。Java 在超大型企业、传统软件行业、金融领域优势更大。目前来看 Node.js、Golang、Python、Ruby 等语言还难以企及 PHP 和 Java。

PHP 语言之所以能有今天的地位，得益于 PHP 语言设计者一直遵从实用主义，将技术的复杂性隐藏在底层。

PHP 语言入门简单，容易掌握，程序健壮性好，不容易出现像 Java、C++ 等其他语言那样复杂的问题，如内存泄漏和 Crash ，跟踪调试相对轻松很多。

PHP 官方提供的标准库非常强大，各种功能函数都能在官方的标准库中找到，包括 MySQL、Memcache、Redis、GD 图形库、CURL、XML、JSON 等等，免除了开发者到处找库的烦恼。

PHP 的文档非常棒，每个函数都有详细的说明和使用示例。第三方类库和工具、代码、项目也很丰富。开发者可以快速、高效地使用 PHP 编写开发各类软件。

到目前为止市面上仍然没有出现比 PHP 更简单易用的编程语言，就连全球著名的网站都在使用 PHP，请看下表：

| **序号** | **网站** | **程序** | **OS（系统）** | **DB（数据库）** |
| 1 | FACEBOOK | PHP | Linux+Apache | MySql |
| 2 | GOOGLE  | Python  | 集群(自主研发) | 集群 |
| 3 | YouTube | Python  | 集群 | 集群 |
| 4 | Yahoo! | PHP | FreeBSD+Apache | MySql |
| 5 | 百度 | PHP | Linux+Apache | 集群 |
| 6 | 维基百科 | PHP | Linux+Apache | MySql |
| 7 | 亚马逊 | CGI | Linux | Oracle |
| 8 | Windows Live | ASP.NET | Windows+IIS | MsSql |
| 9 | 腾讯 QQ | PHP | 集群 | 集群 |
| 10 | 淘宝 | PHP | Linux | Oracle |
| 11 | Blogspot | Python  | 集群 | 集群 |
| 12      | Twitter | Ruby | 未知 | NoSql |
| 13 | LinkedIn    | JSP | 未知 | 未知 |
| 14 | Bing    | ASP.NET | Windows+IIS | MsSql |
| 15 | 新浪 | PHP | Linux+Apache | MySql |
| 16 | Яндекс  | PHP | 集群 | 集群 |
| 17      | MSN | ASP.NET | Windows+IIS | MsSql |
| 18 | ВКонтакте   | PHP | Linux+Apache | MySql |
| 19 | eBay    | ASP.NET | Windows+IIS | Oracle |
| 20 | WordPress   | PHP | Linux+Apache | MySql |
| 21 | 网易 | JSP | Linux+Apache | Oracle |
| 22 | 新浪微博 | PHP | FreeBSD+Apache | MySql |
| 23 | 微软 | ASP.NET | Windows+IIS | MsSql |
| 24 | Tumblr | PHP | Linux+Apache | MySql |
| 25 | Ask | ASP.NET | Windows+IIS | MsSql |
| 26 | Hao123 | PHP | Linux+Apache | MySql |
| 27 | xvideos | 未知 | Nginx   | Redis |
| 28 | Conduit | C#.NET | Windows+IIS | MsSql |
| 29 | Pinterest   | Python  | FreeBSD+Apache | MySql |
| 30 | FC2 | 未知 | 未知 | 未知 |
| 31 | delta-search | Python | 集群(自主研发) | 集群 |
| 32 | Craigslist | PHP | Linux+Apache | MySql |
| 33 | 天猫 | PHP | Linux+Apache | MySql |
| 34 | Babylon | ColdFusion | Windows+IIS | MsSql |
| 35 | 搜狐网 | PHP | Linux+Apache | MySql |
| 36 | PayPal | JSP | 未知 | 未知 |
| 37 | Adobe | AIR | 未知 | 未知 |
| 38 | The Internet Movie Database | ASP.NET | Windows+IIS | MsSql |
| 39 | 苹果 | 未知 | 未知 | 未知 |
| 40 | BBC Online | ASP.NET | Windows+IIS | Oracle |
| 41 | soso 搜搜 | PHP | Linux+Nginx | 未知 |
| 42 | Pornhub | 未知 | 未知 | 未知 |
| 43 | 凤凰网 | PHP | Linux+Apache | MySql |
| 44 | AVG | 未知 | 未知 | 未知 |
| 45 | AOL | JSP | 未知 | 未知 |
| 46 | Blogger | 未知 | 未知 | 未知 |
| 47 | Go | 未知 | 未知 | 未知 |
| 48 | 阿里巴巴 | JSP | Unix+Apache | Oracle |
| 49 | 优酷 | PHP | Linux+Apache | MySql |
| 50 | 360 安全中心 | PHP | Linux+Apache | MySql |

所以 PHP 的前景还是很广阔的，与其纠结于编程语言的选择，不如好好地深入学习使用 PHP 。PHP 的未来将会朝着更加企业化的方向迈进，并且将更适合大型系统的开发。

## PHP 的应用领域

#### 1) WEB 服务端

PHP 尤其适合网站开发，没有任何语言可以与其抗衡（从权威技术网站 w3techs.com 的统计来看，PHP 被 79% 的网站使用）。

当然，PHP 的用武之地不仅仅只是在网站开发，在游戏开发、广告系统开发、API 接口开发、移动端后台开发，内部 OA 系统开发上都能使用 PHP。

如今微信开发异常火爆，PHP 同样是最好的开发工具，更加上 react native 框架和 Hybrid App 的诞生，PHP 程序员更是可以开发手机 App 了。

#### 2) 命令行脚本

我们也可以通过命令行模式来运行 PHP 脚本，这种模式下不需要服务器的支持或者浏览器的触发，在命令行脚本模式下仅需要 PHP 解析器来执行。通常这种方法被用在 Windows 和 Linux 平台下做日常运行脚本使用，如某些守护程序等。当然这些脚本也可以用来处理简单的文本。

#### 3) 编写桌面应用程序

对于开发有图形界面的桌面应用程序来说，PHP 或许不是一种最好的语言，但是如果用户非常精通 PHP，并且希望在客户端应用程序中使用 PHP 的一些高级特性，就可以利用 PHP-GTK 来编写这些程序。

PHP-GTK 是 PHP 的一个扩展，使用 PHP-GTK 可以创建独立的 GUI（用户图形界面）应用程序，例如 mp3 播放器、文本编辑器、数据库后台等等。可以从 http://gtk.php.net/ 下载 PHP-GTK。