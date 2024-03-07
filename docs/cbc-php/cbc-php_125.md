# PHP 获取 Cookie 的值

> 原文：[`c.biancheng.net/view/7604.html`](http://c.biancheng.net/view/7604.html)

前面我们介绍了如何《设置 Cookie》，如果 Cookie 设置成功，客户端就拥有了 Cookie 文件，用来保存 Web 服务器为其设置的用户信息。以 Chrome 浏览器为例，其 Cookie 文件就存放在“C:\Users\用户名\AppData\Local\Google\Chrome\User Data\Default”目录下的 Cookies 文件中。

Cookie 是一个以文本形式记录信息的，当我们再次访问一个网站时，浏览器会自动把与该站点对应的 Cookie 信息全部发送给服务器。

从 PHP5 之后，任何 Cookie 信息都会被自动保存在超全局变量 $_COOKIE 中，所以在每个 PHP 脚本中都可以从 $_COOKIE 中读取相应的 Cookie 信息。$_COOKIE 中存储着所有通过 HTTP 传递的 Cookie 内容，并以 Cookie 的识别名称为索引值、内容值为元素。

在设置 Cookie 的脚本中，第一次读取它的信息并不会生效，必须刷新或下一个执行脚本时才可以看到 Cookie 值，因为 Cookie 要先被设置到浏览器中，当再次访问时才能被发送过来，这时才能被获取。所以要测试一个 Cookie 是否被成功设定，可以再其到期之前通过另外一个页面来访问其的值。

【示例】使用超全局变量 $_COOKIE 获取 Cookie 的值。

```

<?php
    if(!isset($_COOKIE['time'])){                           //检测 Cookie 文件是否存在
        setcookie('time',date('Y-m-d H:i:s'));              //设置一个 Cookie 变量
        echo "第一次访问<br>";
    }else{
        echo "上次访问的时间为：".$_COOKIE['time'].'<br>';    //输出上次访问网站的时间
        setcookie('time',date('Y-m-d H:i:s'),time()+60);      //设置保存 Cookie 失效的时间的变量
    }
    echo "本次访问的时间为：".date('Y-m-d H:i:s');            //输出当前的访问时间
?>
```

首次运行的结果如下：

第一次访问
本次访问的时间为：2020-04-24 10:49:48

再次运行的结果如下：

上次访问的时间为：2020-04-24 10:49:48
本次访问的时间为：2020-04-24 10:50:11