# Docker Swarm 服务日志及相关配置

> 原文：[`c.biancheng.net/view/3181.html`](http://c.biancheng.net/view/3181.html)

Docker Swarm 服务的日志可以通过执行 `docker service logs` 命令来查看，然而并非所有的日志驱动（Logging Driver）都支持该命令。

Docker 节点默认的配置是，服务使用 json-file 日志驱动，其他的驱动还有 journald（仅用于运行有 systemd 的 Linux 主机）、syslog、splunk 和 gelf。

json-file 和 journald 是较容易配置的，二者都可用于 `docker service logs` 命令。

命令格式为：

docker service logs <service-name>

若使用第三方日志驱动，那么就需要用相应日志平台的原生工具来查看日志。

如下是在 daemon.json 配置文件中定义使用 syslog 作为日志驱动的示例。

{
    "log-driver": "syslog"
}

通过在执行 `docker service create` 命令时传入 --logdriver 和 --log-opts 参数可以强制某服务使用一个不同的日志驱动，这会覆盖 daemon.json 中的配置。

服务日志能够正常工作的前提是，容器内的应用程序运行于 PID 为 1 的进程，并且将日志发送给 STDOUT，错误信息发送给 STDERR。日志驱动会将这些日志转发到其配置指定的位置。

如下的 `docker service logs` 命令示例显示了服务 svc1 的所有副本的日志，可见该服务在启动副本时出现了一些错误。

$ docker service logs seastack_reverse_proxy
svc1.1.zhc3cjeti9d4@wrk-2 | [emerg] 1#1: host not found...
svc1.1.6m1nmbzmwh2d@wrk-2 | [emerg] 1#1: host not found...
svc1.1.6m1nmbzmwh2d@wrk-2 | nginx: [emerg] host not found..
svc1.1.zhc3cjeti9d4@wrk-2 | nginx: [emerg] host not found..
svc1.1.1tmya243m5um@mgr-1 | 10.255.0.2 "GET / HTTP/1.1" 302

以上输出内容有删减，不过仍然可以看到来自服务的 3 个副本的日志（两个运行失败，一个运行成功）。

每一行开头为副本名称，其中包括服务名称、副本编号、副本 ID 以及所在的主机。之后是日志消息。

由于输出内容有所删减，因此失败原因较难定位，不过看起来似乎是前两个副本尝试连接另一个启动中的服务而导致失败（一种所依赖的服务未完全启动导致的竞态条件问题）。

对于查看日志命令，可以使用 --follow 进行跟踪、使用 --tail 显示最近的日志，并使用 --details 获取额外细节。