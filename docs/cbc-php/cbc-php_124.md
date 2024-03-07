# PHP 设置 Cookie

> 原文：[`c.biancheng.net/view/7602.html`](http://c.biancheng.net/view/7602.html)

Cookie 的建立十分简单，只要用户的浏览器支持 Cookie 功能，就可以使用 PHP 中的 setcookie() 函数来设置一个 Cookie。

不过在设置 Cookie 之前必须了解的是，Cookie 是 HTTP 响应头的一部分，而响应头必须在页而其他内容之前发送，它必须最先输出。若在 setcookie() 函数前输出一个 HTML 标记或 echo 语句，甚至一个空行都会导致程序出错。

setcookie() 函数的语法格式如下：

setcookie(string $name [, string $value = "" [, int $expire = 0 [, string $path = "" [, string $domain = "" [, bool $secure = false [, bool $httponly = false ]]]]]])

参数说明如下：

*   $name：设置 Cookie 的名称；
*   $value：可选参数，用来设置 Cookie 的值。可以通过 $_COOKIE['$name'] 的形式来获取 $value 的值；
*   $expire：可选参数，用来设置 Cookie 的过期时间，这个时间是 Unix 时间戳的形式。如果设置成零或者忽略该参数，Cookie 会在会话结束时过期（也就是关掉浏览器时）；
*   $path：可选参数，用来设置 Cookie 有效的服务器路径。 设置成 '/' 时，Cookie 对整个域名 $domain 有效。 如果设置成'/foo/'，则 Cookie 仅仅对 $domain 中 /foo/ 目录及其子目录有效（比如 /foo/bar/）。默认值为设置 Cookie 时的目录；
*   $domain：可选参数，用来设置 Cookie 的有效域名/子域名。设置成子域名（例如 'c.biancheng.net'），会使 Cookie 对这个子域名和它的三级域名有效（例如 php.c.biancheng.net）。 要让 Cookie 对整个域名有效（包括它的全部子域名），只要设置成域名就可以了（例如 'biancheng.net'）；
*   $secure：可选参数，用来设置这个 Cookie 是否仅仅通过安全的 HTTPS 连接传给客户端。设置成 TRUE 时，只有安全连接存在时才会设置 Cookie；
*   $httponly：可选参数，设置成 TRUE 时，Cookie 仅可通过 HTTP 协议访问，也就是说 Cookie 无法通过类似 JavaScript 这样的脚本语言访问。设置该参数可以有效的减少受到 XSS 攻击的风险。

注意：如果在调用 setcookie() 函数以前产生了输出，setcookie() 会调用失败并返回 FALSE。 如果 setcookie() 成功运行，则会返回 TRUE。

【示例】使用 setcookie() 函数创建 Cookie。

```

<?php
    setcookie('Website', 'C 语言中文网');
    setcookie('Title', 'Cookie', time()+3600);  // 设置 Cookie 1 小时后过期
    setcookie('Url', 'http://c.biancheng.net/php/', time()+3600, '/', 'c.biancheng.net', false);
    echo '<pre>';
    print_r($_COOKIE);
?>
```

运行结果如下：

Array
(
    [Website] => C 语言中文网
    [Title] => Cookie
    [Url] => http://c.biancheng.net/php/
)

提示：当第一次运行这个脚本文件时并不会有任何输出信息，因此设置完 Cookie 后需要刷新一下页面，这样在下次请求时 HTTP 头部才会携带上一次设置的 Cookie 信息，这时才能读取到 Cookie。