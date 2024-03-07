# Nginx rewrite 重定向配置详解

> 原文：[`www.weixueyuan.net/a/655.html`](http://www.weixueyuan.net/a/655.html)

访问重写 rewrite 是 Nginx HTTP 请求处理过程中的一个重要功能，它是以模块的形式存在于代码中的，其功能是对用户请求的 URI 进行 PCRE 正则重写，然后返回 30× 重定向跳转或按条件执行相关配置。

rewrite 模块内置了类似脚本语言的 set、if、break、return 配置指令，通过这些指令，用户可以在 HTTP 请求处理过程中对 URI 进行更灵活的操作控制。rewrite 模块提供的指令可以分两类，一类是标准配置指令，这部分指令只是对指定的操作进行相应的操作控制；另一类是脚本指令，这部分指令可以在 HTTP 指令域内以类似脚本编程的形式进行编写。

## 1、标准配置指令

常用的标准配置指令如下面表格所示。

表：rewrite 日志记录指令

| 名称 | rewrite 日志记录指令 |
| 指令 | rewrite_log |
| 作用域 | http, server, location |
| 默认值 | off |
| 指令值选项 | on 或 off |
| 指令说明 | 当指令值为 on 时，rewrite 的执行结果会以 notice 级别记录到 Nginx 的 error 日志文件中 |

配置样例如下：

http {
   rewrite_log off;
}

表：未初始化变量告警日志记录指令

| 名称  | 未初始化变量告警日志记录指令 |
| 指令 | uninitialized_variable_warn |
| 作用域 | http, server, location |
| 默认值 | on |
| 指令值选项 | on 或 off |
| 指令说明 | 指令值为 on 时，会将未初始化的变量告警记录到日志中 |

配置样例如下：

http {
    uninitialized_variable_warn off;
}

表：rewrite 指令

| 名称 | rewrite 指令 |
| 指令 | rewrite |
| 作用域 | server, location |
| 默认值 | on |
| 指令值选项 | on 或 off |
| 指令说明 | 对用户的 URI 用正则表达式的方式进行重写，并跳转到新的 URI |

配置样例如下：

http {
    rewrite ^/users/(.*)$ /show?user=$1 last;
}

rewrite 访问重写是通过 rewrite 指令实现的，rewrite 指令的语法格式如下：

rewrite regex replacement [flag];

1) regex 是 PCRE 语法格式的正则表达式。

2) replacement 是重写 URI 的改写规则。当改写规则以“http://”“https://”或“$scheme”开头时，Nginx 重写该语句后将停止执行后续任务，并将改写后的 URI 跳转返回客户端。

3) flag 是执行该条重写指令后的操作控制符。操作控制符有如下 4 种：

*   last：执行完当前重写规则跳转到新的 URI 后继续执行后续操作；
*   break：执行完当前重写规则跳转到新的 URI 后不再执行后续操作。不影响用户浏览器 URI 显示；
*   redirect：返回响应状态码 302 的临时重定向，返回内容是重定向 URI 的内容，但浏览器网址仍为请求时的 URI；
*   permanent：返回响应状态码 301 的永久重定向，返回内容是重定向 URI 的内容，浏览器网址变为重定向的 URI。

## 2、脚本指令

常见的脚本指令如下面表格所示。

表：设置变量指令

| 名称 | 设置变量指令 |
| 指令 | set |
| 作用域 | server, location, if |
| 指令说明 | set 指令，可以用来定义变量 |

配置样例如下：

http {
    server{
        set $test "check";
    }
}

http{
    server {
        listen 8080;
        location /foo {
            set $a hello;
            rewrite ^ /bar;
        }
        location /bar {
            # 如果这个请求来自“/foo”,$a 的值是“hello”。如果直接访问“/bar”，$a 的值为空
            echo “a = [$a]”; 
        }
    }
}

用 set 指令创建变量后，变量名是 Nginx 配置全局域可用的，但变量值只在有该变量赋值操作的 HTTP 处理流程中可用。

http{
    server {
        listen 8080;
        location /foo {
            set $a hello;
            rewrite ^ /bar;
        }
        location /bar {
            # 如果这个请求来自“/foo”,$a 的值是“hello”。如果直接访问“/bar”，$a 的值为空
            if ( $a = “hello” ){
                rewrite ^ /newbar;
            }
        }
    }
}

当 set 指令后只有变量名时，系统会自动创建该变量，变量值为空。

http {
    server{
        set $test;
    }
}

变量插值如下：

http {
    server{
        set $test "check ";
        if ( "${test}nginx" = "nginx" ){ #${test}nginx 的值为"check nginx"

        }
    }
}

表：条件判断指令

| 名称 | 条件判断指令 |
| 指令 | if |
| 作用域 | server, location |
| 指令说明 | 条件判断指令 |

配置样例如下：

http {
    server {
        if ($http_cookie ~* "id=([^;]+)(?:;|$)") {
            set $id $1;
        }
    }
}

1) 当判断条件为一个变量时，变量值为空或以 0 开头的字符串都被判断为 false。

2) 变量内容字符串比较操作运算符为“=”或“!=”。

3) 进行正则表达式比较时，有以下 4 个操作运算符：

*   “~”：区分大小写匹配；
*   “~*”：不区分大小写匹配；
*   “!~”：区分大小写不匹配；
*   “!~*”：不区分大小写不匹配。

4) 进行文件或目录比较时，有以下 4 个操作运算符：

*   “-f”：判断文件是否存在，可在运算符前加“!”表示反向判断。
*   “-d”：判断目录是否存在，可在运算符前加“!”表示反向判断。
*   “-e”：判断文件、目录或链接符号是否存在，可在运算符前加“!”表示反向判断。
*   “-x”：判断文件是否为可执行文件，可在运算符前加“!”表示反向判断。

表：终止指令

| 名称  | 终止指令 |
| 指令 | break |
| 作用域  | server, location, if |
| 指令说明 | 终止后续指令的执行 |

配置样例如下：

http {
    server {
        if ($slow) {
            limit_rate 10k;
            break;
        }
    }
}

表：跳转指令

| 名称 | 跳转指令 |
| 指令 | return |
| 作用域 | server, location, if |
| 指令说明 | 向客户端返回响应状态码或执行跳转 |

配置样例如下：

http {
    server {
        if ($request_method = POST) {
            return 405;
        }
    }
}

1) return 的指令值有以下 4 种方式。

*   return code：向客户端返回指定 code 的状态码，当返回非标准的状态码 444 时，Nginx 直接关闭连接，不发送响应头信息。
*   return code text：向客户端发送带有指定 code 状态码和 text 内容的响应信息。因要在客户端显示 text 内容，所以 code 不能是 30×。
*   return code URL：这里的 URL 可以是内部跳转或变量 $uri，也可以是有完整 scheme 标识的 URL，将直接返回给客户端执行跳转，code 只能是 30×。
*   return URL：此时默认 code 为 302，URL 必须是有完整 scheme 标识的 URL。

2) return 也可以用来调试输出 Nginx 的变量。