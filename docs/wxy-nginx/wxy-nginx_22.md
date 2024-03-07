# Nginx 日志记录配置：log_not_found、log_subrequest

> 原文：[`www.weixueyuan.net/a/673.html`](http://www.weixueyuan.net/a/673.html)

Nginx 中的日志记录指令如下面表格所示。

表：不存在文件日志指令

| 名称 | 不存在文件日志指令 |
| 指令 | log_not_found |
| 作用域 | http、server、location |
| 默认值 | on |
| 指令值选项 | on 或 off |
| 指令说明 | 用于设定如果文件不存在错误是否写入日志 |

配置样例如下：

http {
   log_not_found on;
}

表：子请求访问日志指令

| 名称 | 子请求访问日志指令 |
| 指令 | log_subrequest |
| 作用域 | http、server、location |
| 默认值 | on |
| 指令值选项 | on 或 off |
| 指令说明 | 用于设定子请求的访问记录是否写入日志 |

配置样例如下：

http {
   log_subrequest on;
}