# PHP 清除 Cookie

> 原文：[`c.biancheng.net/view/7607.html`](http://c.biancheng.net/view/7607.html)

当 Cookie 被创建后，如果没有设置它的失效时间，其 Cookie 文件会在关闭浏览器时被自动删除，如果要在关闭浏览器之前删除 Cookie 文件，同样需要使用 setcookie() 函数。

删除 Cookie 和创建 Cookie 的方式类似，只需要使用 setcookie() 函数将 Cookie 的值（也就是第二个参数）设置为空，或者将 Cookie 的过期时间（也就是第三个参数）设置为小于系统的当前时间即可。

【示例】使用 setcookie() 函数将 Cookie 的值设置为空的方式来清除 Cookie。

```

<?php
    echo '<pre>';
    if(!isset($_COOKIE['url']) && !isset($_COOKIE['name'])){
        setcookie('url','http://c.biancheng.net/php/');
        setcookie('name','C 语言中文网');
        echo '首次运行，设置 url、name 两个 Cookie 的值';
    }else if(isset($_COOKIE['url'])){
        echo '查看 Cookie 的值，如下所示：<br>';
        print_r($_COOKIE);
        echo '清除 url 的值';
        setcookie('url','');
    }else{
        print_r($_COOKIE);
    }
?>
```

首次运行上面的代码会创建名为 url、name 的两个 Cookie；再次运行可以查看 Cookie 的值，并清除其中 url 的值；第三次运行可以查看清除后的结果。如下所示：

// 第一次运行
首次运行，设置 url、name 两个 Cookie 的值
// 第二次运行
查看 Cookie 的值，如下所示：
Array
(
    [url] => http://c.biancheng.net/php/
    [name] => C 语言中文网
)
清除 url 的值
// 第三次运行
Array
(
    [name] => C 语言中文网
)

【示例】通过将 Cookie 的过期时间（也就是第三个参数）设置为小于系统的当前时间的方式清除 Cookie。

```

<?php
    echo '<pre>';
    if(!isset($_COOKIE['url']) && !isset($_COOKIE['name'])){
        setcookie('url','http://c.biancheng.net/php/');
        setcookie('name','C 语言中文网');
        echo '首次运行，设置 url、name 两个 Cookie 的值';
    }else if(isset($_COOKIE['url'])){
        echo '查看 Cookie 的值，如下所示：<br>';
        print_r($_COOKIE);
        echo '清除 url 的值';
        setcookie('url','http://c.biancheng.net/php/', time()-1);
    }else{
        print_r($_COOKIE);
    }
?>
```

运行结果如下：

// 第一次运行
首次运行，设置 url、name 两个 Cookie 的值
// 第二次运行
查看 Cookie 的值，如下所示：
Array
(
    [url] => http://c.biancheng.net/php/
    [name] => C 语言中文网
)
清除 url 的值
// 第三次运行
Array
(
    [name] => C 语言中文网
)