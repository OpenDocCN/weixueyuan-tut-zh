# JSP 与 ASP、PHP 的比较

现在，最常用的动态网页设计语言有 ASP（Active Server Pages）、JSP（Java Server Pages）和 PHP（Hypertext Preprocessor）。

## ASP

ASP 釆用脚本语言 VBScript（JavaScript）作为自己的开发语言。ASP 是 Microsoft 开发的动态网页语言，也继承了微软产品的一贯传统，只能在微软的服务器产品 IIS（Internet Information Server）上执行。

ASP 是 Web 服务器端的开发环境，可以产生和执行动态的、交互的、局效的 Web 服务应用程序。 其技术特点主要有以下几个方面：

*   与浏览器无关（Browser Independence），客户端只要使用可执行 HTML 码的浏览器，即可浏览 Active Server Pages 所设计的网页内容。Active Server Pages 所使用的脚本语言（VBScript、JScript）均在 Web 服务器端执行，客户端的浏览器不需要执行这些脚本语言。
*   Active Server Pages 能与任何 ActiveX Scripting 语言兼容。除了可使用 VBScript 或 JScript 语言设计外，还可以通过 plug-in 方式，使用由第三方提供的其他脚本语言，如 REXX、Perl、Tel 等。脚本引擎是处理脚本程序的 COM（Component Object Model）对象。
*   使用 VBScript、JScript 等简单易懂的脚本语言，结合 HTML 代码，即可快速地编写出网站的应用程序。可使用服务器端的脚本来产生客户端的脚本。
*   使用普通的文本编辑器，如 Windows 的记事本，即可进行程序设计，无须编译，容易编写，可在服务器端直接执行。

## PHP

PHP 是跨平台的服务器端的嵌入式脚本语言。它几乎都要借用 C、Java 和 Perl 语言的语法，同时结合 PHP 自己的特性，使得 Web 开发者能够快速地写出动态页面。PHP 的特点是：支持绝大多数数据库，并且其源码是完全公开的。

PHP 可在 Windows、Unix、Linux 的 Web 服务器上正常执行，还支持 IIS、Apache 等一般的 Web 服务器，用户更换平台时，无需变换 PHP 代码。

PHP 与 MySQL 是目前绝佳的组合。用户还可以自己编写外围的函数间接存取数据库，通过这样的途径，在更换使用的数据库时，可以轻松地修改编码以适应 这样的变化。

#### 提示：

PHP LIB 就是最常用的可以提供一般事务需要的一系列基库。但 PHP 提供的数据库接口支持彼此不够统一。

## JSP

JSP 同 PHP 类似，几乎可以在所有平台上执行，如 Windows、Linux、Unix。Web 服务器 Apache 已经能够支持 JSP，而 Apache 广泛应用在 Windows、Unix 和 Linux 上，因此 JSP 有更广泛的执行平台。

虽然现在 Windows 操作系统占了很大的市场份额，但是在服务器方面 Unix 的优势仍然很大，而新崛起的 Linux 更是来势不小。

从一个平台移植到另外一个平台时，JSP 和 JavaBean 甚至不用重新编译，因为 Java 字节码都是标准的，与平台无关。 ASP、PHP、JSP 三者都是面向 Web 服务器的技术，客户端浏览器不需要任何附加的软件支持。

普通的 HTML 页面只依赖于 Web 服务器，但 ASP、PHP、JSP 页面需要附加的语言引擎分析和执行程序代码。程序代码的执行结果被重新嵌入 HTML 代码中，然后一起发送给浏览器。 三者都提供了在 HTML 代码中混合某种程序代码、由语言引擎解释执行程序代码的能力。JSP 代码被编译成 Servlet 并由 Java 虚拟机解释执行，这种编译操作仅在对 JSP 页面的第一次请求时发生。

在 ASP、PHP、JSP 环境下，HTML 代码主要负责描述信息的显示样式，而程序代码则用来描述处理逻辑。