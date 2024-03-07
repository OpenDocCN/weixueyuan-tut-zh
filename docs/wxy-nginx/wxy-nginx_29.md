# Nginx index：首页处理

> 原文：[`www.weixueyuan.net/a/695.html`](http://www.weixueyuan.net/a/695.html)

HTTP 请求经过一系列的请求流程处理后，最终将读取数据并把数据内容返回给用户。当用户请求没有明确指定请求的文件名称时，Nginx 会根据设定返回默认数据，实现这一功能包含 ngx_http_index_module、ngx_http_random_index_module、ngx_http_autoindex_module 这 3 个模块。

常用的首页处理配置指令如下面表格中所示。

表：首页指令

| 名称 | 首页指令 |
| 指令  | index |
| 作用域 | http、server、location |
| 默认值 | index index.html |
| 指令说明 | 设置 HTTP 服务器的默认首页 |

配置样例如下：

location / {
    index index.$geo.html index.html;
}

指令值为多个文件时，会按照从左到右的顺序依次查找，找到对应文件后将结束查找。

表：随机首页指令

| 名称 | 随机首页指令 |
| 指令 | random_index |
| 作用域 | location |
| 默认值 | off |
| 指令值可选项 | on 或 off |
| 指令说明 | 随机读取文件目录下的文件内容为首页内容 |

配置样例如下：

root /opt/nginx-web/html;
location / {
    random_index on;
}

该指令的执行优先级高于 index 指令，文件目录中的隐藏文件将被忽略。

表：自动首页指令

| 名称 | 自动首页指令 |
| 指令  | autoindex |
| 作用域 | http、server、location |
| 默认值 | off |
| 指令值可选项 | on 或 off |
| 指令说明 | 自动创建目录文件列表为目录首页 |

表：自动首页格式指令

| 名称 | 自动首页格式指令 |
| 指令 | autoindex_format |
| 作用域 | http、server、location |
| 默认值 | html |
| 指令值可选项 | html 或 xml 或 json 或 jsonp |
| 指令说明 | 设置 HTTP 服务器的自动首页文件格式 |

表：自动首页文件大小指令

| 名称  | 自动首页文件大小指令 |
| 指令 | autoindex_exact_size |
| 作用域 | http、server、location |
| 默认值 | on |
| 指令值可选项 | on 或 off |
| 指令说明 | 设置 HTTP 服务器的自动首页显示文件大小。默认文件大小单位为 Byte，当指令值为 off 时，将根据文件大小自动换算为 KB 或者 MB 或者 GB 的单位大小 |

表：自动首页时间指令

| 名称 | 自动首页时间指令 |
| 指令 | autoindex_localtime |
| 作用域 | http、server、location |
| 默认值 | off |
| 指令值可选项 | on 或 off |
| 指令说明 | 按照服务器时间显示文件时间。默认显示的文件时间为 GMT 时间。当指令值为 on 时，显示的文件时间为服务器时间 |

配置样例如下：

location / {
    autoindex on;
    autoindex_format html;
    autoindex_exact_size off;
    autoindex_localtime on;
}