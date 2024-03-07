# JSP 页面的组成及其元素

## JSP 页面的组成

在 HTML 页面文件中加入 Java 程序段和 JSP 标签，即可构成一个 JSP 页文件，JSP 页面由 5 种元素组合而成。

1.  普通的 HTML 标记符。
2.  JSP 标签，如指令标签、动作标签。
3.  变量和方法的声明。
4.  Java 程序段。
5.  Java 表达式。

当服务器上的 JSP 页面被第一次请求执行时，服务器上的 JSP 引擎首先将 JSP 页面文件转译成 Java 文件，再将 Java 文件编译，生成字节码文件，然后通过执行字节码文件响应客户的请求，这个字节码文件的任务如下。

*   把 JSP 页面中普通的 HTML 标记符号交给客户的浏览器执行并显示。
*   JSP 标签、数据和方法声明、Java 程序段由服务器负责执行，将需要显示的结果发送给客户的浏览器。
*   Java 表达式由服务器负责计算，并将结果转化为字符串，然后交给客户的浏览器负责显示。

## JSP 页面中的元素

Java Server Pages（JSP）能够分离页面的静态 HTML 和动态部分。HTML 可以用任何通常使用的 Web 制作工具编写，编写方式也和原来的一样；动态部分的代码放入特殊标记之内，大部分以“<%”开始，以“%>”结束。

例如，下面是一个 JSP 页面的片断，如果用`http://host/test.jsp?title=Core+Web+Programming`这个 URL 打开该页面，则结果显示 “Thanks for ordering Core Web Programming”。 test.jsp 源程序如下：

```
Thanks for ordering
<i><%= request.getParameter("title") %></i>
```

JSP 页面文件通常以 .jsp 为扩展名，而且可以安装到任何能够存放普通 Web 页面的地方。虽然从代码编写来看，JSP 页面更像普通 Web 页面而不像 Servlet，但实际上，JSP 最终会被转换成正规的 Servlet，静态 HTML 直接输出到和 Servlet service 方法关联的输出流。

JSP 到 Servlet 的转换过程一般在出现第一次页面请求时进行。因此，如果希望第一个用户不会由于 JSP 页面转换成 Servlet 而等待太长的时间，并且希望确保 Servlet 已经正确地编译并装载，你可以在安装 JSP 页面之后自己请求这个页面，这样 JSP 页面就转换成 Servlet 了。

另外也请注意，许多 Web 服务器允许定义别名，所以一个看起来指向 HTML 文件的 URL 实际上可能指向 Servlet 或 JSP 页面。

除了普通 HTML 代码之外，嵌入 JSP 页面的其他成分主要有三种：脚本元素（Scripting Element）、指令（Directive）和动作（Action）。

脚本元素用来嵌入 Java 代码，这些 Java 代码将成为转换得到的 Servlet 的一部分；JSP 指令用来从整体上控制 Servlet 的结构；动作用来引入现有的组件或者控制 JSP 引擎的行为。为了简化脚本元素，JSP 定义了一组可以直接使用的变量（预定义变量）。