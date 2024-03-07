# PHP 预定义变量

> 原文：[`c.biancheng.net/view/7262.html`](http://c.biancheng.net/view/7262.html)

预定义变量又叫超全局变量，它们不需要提前声明就可以在所有的作用域中使用。通过这些预定义变量可以获取用户会话、用户操作系统的环境和本地操作系统的环境等信息。

注意：预定义变量基本上都是以数组形式存在的。

常用的预定义变量如下所示：

*   $GLOBALS：全局作用域中的全部可用变量；
*   $_SERVER：服务器和执行环境的信息；
*   $_REQUEST：包含了 $_GET，$_POST 和 $_COOKIE 的所有信息；
*   $_POST：通过 POST 方法提交的数据；
*   $_GET：通过 GET 方法提交的数据；
*   $_FILES：通过 POST 方式上传到服务器的文件数据；
*   $_ENV：通过环境方式传递给当前脚本的变量组成的数组；
*   $_COOKIE：通过 HTTP Cookies 方式传递给当前脚本的变量所组成的数组；
*   $_SESSION：当前脚本可用 SESSION 变量组成的数组。

## $GLOBALS

$GLOBALS 是一个包含了全局作用域中所有变量的数组，变量的名字就是数组的键。

注意：$GLOBALS 中并不包含函数中的局部变量和静态变量。

示例代码如下：

```

<?php
    $name = 'C 语言中文网';
    $title = 'PHP 预定义变量';
    function demo(){
        static $str = 'PHP 教程';
        $url = 'http://c.biancheng.net/php/';
    }
    echo "<pre>";
    var_dump($GLOBALS);
?>
```

运行结果如下：

array(7) {
  ["_GET"]=>
  array(0) {
  }
  ["_POST"]=>
  array(0) {
  }
  ["_COOKIE"]=>
  array(0) {
  }
  ["_FILES"]=>
  array(0) {
  }
  ["GLOBALS"]=>
  array(7) {
    ["_GET"]=>
    array(0) {
    }
    ["_POST"]=>
    array(0) {
    }
    ["_COOKIE"]=>
    array(0) {
    }
    ["_FILES"]=>
    array(0) {
    }
    ["GLOBALS"]=>
    *RECURSION*
    ["name"]=>
    string(16) "C 语言中文网"
    ["title"]=>
    string(19) "PHP 预定义变量"
  }
  ["name"]=>
  string(16) "C 语言中文网"
  ["title"]=>
  string(19) "PHP 预定义变量"
}

## $_SERVER

$_SERVER 是一个包含了诸如头信息（header）、路径（path）、以及脚本位置（script locations）等等信息的数组。这个数组中的所有项目由 Web 服务器创建。

根据服务器环境的不同，$_SERVER 中的信息也有所不同，我们可以直接使用 var_dump($_SERVER) 来打印 $_SERVER 中的信息，如下所示：

```

<?php
    echo "<pre>";
    var_dump($_SERVER);
?>
```

运行结果如下：

array(42) {
  ["PATH"]=>
  string(347) "C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;C:\Windows\System32\OpenSSH\;D:\install\Go\bin;D:\Gopath\bin;D:\install\Git\cmd;D:\install\MinGW\bin;D:\install\phpstudy\Extensions\php\php7.3.4nts;C:\Users\79330\AppData\Local\Microsoft\WindowsApps;C:\Program Files\Bandizip\;C:\Users\79330\go\bin"
  ["SYSTEMROOT"]=>
  string(10) "C:\Windows"
  ["REMOTE_PORT"]=>
  string(5) "63839"
  ["SCRIPT_FILENAME"]=>
  string(33) "D:/install/phpstudy/WWW/index.php"
  ["SERVER_ADMIN"]=>
  string(17) "admin@example.com"
  ["CONTEXT_DOCUMENT_ROOT"]=>
  string(23) "D:/install/phpstudy/WWW"
  ["REQUEST_SCHEME"]=>
  string(4) "http"
  ["DOCUMENT_ROOT"]=>
  string(23) "D:/install/phpstudy/WWW"
  ["REMOTE_ADDR"]=>
  string(3) "::1"
  ["SERVER_PORT"]=>
  string(2) "80"
  ["SERVER_ADDR"]=>
  string(3) "::1"
  ["SERVER_NAME"]=>
  string(9) "localhost"
  ["SERVER_SOFTWARE"]=>
  string(73) "Apache/2.4.39 (Win64) OpenSSL/1.1.1b mod_fcgid/2.3.9a mod_log_rotate/1.02"
}

由于 $_SERVER 中包含的信息众多，这里只截取了部分结果，部分变量的含义如下表所示：

| 变量的名称 | 说明 |
| $_SERVER['SERVER_ADDR'] | 当前程序所在的服务器的 IP 地址 |
| $_SERVER['SERVER_NAME'] | 当前程序所在的服务器的主机名。如果程序运行在虚拟主机上，则该名称由虚拟主机所设置的值决定 |
| $_SERVER['REQUERT_METHOD'] | 访问页面使用的请求方法。如 GET、HEAD、POST、PUT 等 |
| $_SERVER['REMOTE_ADDR'] | 浏览当前页面的用户的 IP 地址 |
| $_SERVER['REMOTE_HOST'] | 浏览当前页面的用户的主机名，反向域名解析基于该用户的 REMOTE_ADDR |
| $_SERVER['REMOTE_PORT'] | 用户机器上连接到 Web 服务器所使用的端口号 |
| $_SERVER['SCRIPT_FILENAME'] | 当前程序的绝对路径 |
| $_SERVER['SERVER_PORT'] | 当前运行脚本所在的服务器的端口号，默认是 80，如果使用 SSL 安全连接，则这个值是用户设置的 HTTP 端口 |
| $_SERVER['SERVER_SIGNATURE'] | 包含了服务器版本和虚拟主机名的字符串 |
| $_SERVER['DOCUMENT_ROOT'] | 当前运行脚本所在的文档根目录 |

关于其它的预定义变量，现阶段只要认识即可，后面我们还会详细介绍。