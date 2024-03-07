# Cookie 在浏览器中是如何存储的？

> 原文：[`c.biancheng.net/view/6303.html`](http://c.biancheng.net/view/6303.html)

《PHP Cookie 入门教程》中说过，Cookie 是存储在客户端的一段数据，但是不同的浏览器存储 Cookie 的地方不同：

*   一种是将 Cookie 数据保存在文件中；
*   另一种是保存在浏览器内存中。

本节以 Windows 7 系统上的常用浏览器为例来说明 Cookie 的存储位置。

#### IE 浏览器

Windows 系统上 IE 浏览器 Cookie 数据位于 %APPDATA%\Microsoft\Windows\Cookies\ 目录中的 xxx.txt 文件，里面可能有很多个. txt Cookie 文件，如 C：\Users\yren9\AppData\Roaming\Microsoft\Windows\Cookies\0WQ6YROK.txt。

在 IE 浏览器中，IE 将各个站点的 Cookie 分别保存为一个 XXX.txt 这样的纯文本文件；

#### Firefox 和 Chrome 浏览器

Firefox 和 Chrome 是将所有的 Cookie 都保存在一个文件中，该文件的格式为 SQLite 数据库格式的文件。

Firefox 的 Cookie 数据位于 %APPDATA%\Mozilla\Firefox\Profiles\ 目录中的 xxx.default 目录下，名为 Cookies.sqlite 的文件中，如 C：\Users\jay\AppData\Roaming\Mozilla\Firefox\Profiles\ji4grfex.default\cookies.sqlite。

在 Firefox 中查看 Cookie，可以选择“工具>选项>隐私>显示 Cookie”。

Chrome 的 Cookie 数据位于 %LOCALAPPDATA%\Google\Chrome\User Data\Default\ 目录中名为 Cookies 的文件中，如 C：\Users\jay\AppData\Local\Google\Chrome\User Data\Default\Cookies。