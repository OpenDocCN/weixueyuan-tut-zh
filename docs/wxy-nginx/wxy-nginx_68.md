# Nginx error_log：错误日志配置

> 原文：[`www.weixueyuan.net/a/797.html`](http://www.weixueyuan.net/a/797.html)

Nginx 的错误日志可以帮助用户及时判断 Nginx 配置及运行时出错的原因，错误日志也可以通过 Nginx 内置指令进行配置，但不支持格式定义。配置指令如下表所示。

| 说明  | 错误日志指令组成 |
| 主指令 | error_log |
| 作用域 | main、http、mail、stream、server、location |
| 默认值 | logs/error.log error; |
| 指令说明 | 设置错误日志输出方式及输出日志级别 |

关于 error_log 指令有以下几点需要说明：

*   在同一级别的指令域中，也可指定多个日志；
*   指令值中的第一个参数是输出日志的方式，默认是输出到本地的文件中。该指令也支持输出到 syslog 或内存缓冲区中；

error_log syslog:server=192.168.2.109 error;
error_log memory:32m debug;
error_log /dev/null;

# 访问文件不存在时，记入错误日志
log_not_found on;

指令值中第二个参数是输出日志的级别，指定的级别将包含自身及级别值比其小的所有级别日志，日志内容会保存到第一个参数设定的输出位置。

错误日志级别及相关说明如下表所示。

| 级别 | 级别值 | 级别说明 |
| debug | 8 | 代码中标记为 NGX_LOG_DEBUG 的输出，输出最为详细，配合调试使用 |
| info | 7 | 代码中标记为 NGX_LOG_INFO 的输出，因包括除 debug 级别的所有输出，故同样会消耗大量磁盘 IO 资源 |
| notice | 6 | 代码中标记为 NGX_LOG_NOTICE 的输出 |
| warn  | 5 | 代码中标记为 NGX_LOG_WARN 的输出 |
| error | 4 | 代码中标记为 NGX_LOG_ERROR 的输出，实际生产环境中常用的输出级别 |
| crit | 3 | 代码中标记为 NGX_LOG_CRIT 的输出 |
| alert | 2 | 代码中标记为 NGX_LOG_ALERT 的输出 |
| emerg | 1 | 代码中标记为 NGX_LOG_EMERG 的输出 |