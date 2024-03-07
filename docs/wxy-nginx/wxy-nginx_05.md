# Tengine 编译安装

> 原文：[`www.weixueyuan.net/a/608.html`](http://www.weixueyuan.net/a/608.html)

Tengine 是由淘宝网发起的 Web 服务器项目。它在 Nginx 的基础上，针对大访问量网站的需求，添加了很多高级功能和特性。Tengine 的性能和稳定性已经在大型的网站如淘宝网，天猫商城等得到了很好的检验。它的最终目标是打造一个高效、稳定、安全、易用的 Web 平台。

## 1、Tengine 源码获取

Tengine 目前的版本是 Tengine 2.3.2，据其官网介绍，该版本继承了 Nginx 1.17.3 版本的所有特性，并兼容了 Nginx 的配置参数。Tengine 开发了很多自有模块，同时也集成了很多优秀的第三方模块，源代码可以通过 Tengine 的官方网站获得，获取命令如下：

mkdir -p /opt/data/source
cd /opt/data/source
wget http://tengine.taobao.org/download/tengine-2.3.2.tar.gz
tar zxmf tengine-2.3.2.tar.gz

## 2、编译配置参数

Tengine 比开源版 Nginx 增加了一些编译配置参数。Tengine 增加的编译配置参数如下表所示。

| 编译配置参数 | 默认编译状态 | 参数说明 |
| --without-procs | 编译 | 不编译 Procs 模块 |
| --without-http_ssl_module | 编译 | 不编译 HTTP SSL 支持模块 |
| --without-http_stub_status_module | 编译 | 不编译运行状态模块 |
| --without-http-upstream-rbtree | 编译 | 不使用红黑树（RBTree）方式进行上游服务器的查找 |
| --with-http_lua_module | 不编译 | 编译 Lua 脚本模块 |
| --with-stream_sni | 不编译 | 编译 TCP 代理时基于 SSL 的 SNI 支持 |
| --with-jemalloc | 不编译 | 启用 jemalloc 内存管理 |

对于上表，有以下两点说明。

*   jemalloc 是 Facebook 开源的一个内存管理分配器；
*   Nginx 原有编译配置参数参见《Nginx 编译安装》一节。

## 3、代码编译

代码编译过程如下。

# 安装编译依赖
yum -y install gcc pcre-devel  zlib-devel openssl-devel libxml2-devel \
    libxslt-devel gd-devel GeoIP-devel yajl-devel jemalloc-devel \
    libatomic_ops-devel luajit luajit-devel perl-devel perl-ExtUtils-Embed

# 执行编译配置
./configure

# 代码编译及安装
make & make install

安装 Lua 或 LuaJIT 都可以，LuaJIT 是 Lua 的高效版本，推荐安装 LuaJIT。编译完成后，默认安装目录为 /usr/local/nginx。

## 4、Tengine 集成的模块

Tengine 自带的模块都存储在源码目录的 modules 文件中，用户可根据需要通过编译配置参数 `--add-module` 进行选择。模块说明如下表所示。

| 模块文件夹名 | 模块说明 |
| mod_dubbo | 提供对与后端 Dubbo 服务体系对接的支持 |
| ngx_backtrace_module | 该模块可用于在工作进程异常退出时转储 Nginx 的回溯信息，如在接收到某些信号（sigabr、sigbus、sigfpe、sigill、sigiot、sigsegv）时。它非常便于调试 |
| ngx_debug_pool | 该模块可以提供 Nginx/Tengine 内存池占用内存的状态信息 |
| ngx_debug_timer | 该模块可以提供 Nginx/Tengine 定时器的状态信息 |
| ngx_http_concat_module | 类似于 Apache 中的 mod_concat 模块，用于将多个文件合并在一个响应报文中 |
| ngx_http_footer_filter_module | 在请求的响应末尾输出一段内容 |
| ngx_http_lua_module | Lua 脚本集成模块 |
| ngx_http_proxy_connect_module | 提供对 HTTP 的 CONNECT 方法的支持 |
| ngx_http_reqstat_module | 监控模块 |
| ngx_http_slice_module | 文件切片模块 |
| ngx_http_sysguard_module | 该模块监控内存（含 SWAP 分区）、CPU 和请求的响应时间，当某些监控指标达到设定的阈值时，跳转到指定的 URL |
| ngx_http_tfs_module | 该模块实现了 TFS 的客户端，为 TFS 提供了 RESTful API。TFS 的全称是 Taobao File System，是淘宝的一个开源分布式文件系统 |
| ngx_http_trim_filter_module | 该模块用于删除 HTML、内嵌在 JavaScript 和 CSS 中的注释以及重复的空白符 |
| ngx_http_upstream_check_module | 该模块可以为 Tengine 提供主动式后端服务器健康检查功能 |
| ngx_http_upstream_consistent_hash_module | 该模块提供一致性 hash 作为负载均衡算法 |
| ngx_http_upstream_dynamic_module | 此模块可在运行时动态解析 upstream 中 Server 域名 |
| ngx_http_upstream_dyups_module | upstream 动态修改模块 |
| ngx_http_upstream_session_sticky_module | 该模块是一个负载均衡模块，通过 cookie 实现客户端与后端服务器的会话保持，在一定条件下可以保证同一个客户端访问的是同一个后端服务器 |
| ngx_http_upstream_vnswrr_module | 该模块是一个高效的负载均衡算法，同 Nginx 官方的加权轮询算法 SWRR 相比，VNSWRR 具备平滑、散列和高性能特征 |
| ngx_http_user_agent_module | 该模块可以分析 HTTP 消息头属性字段“User-Agent”中的内容 |
| ngx_multi_upstream_module | Dubbo 服务的多路复用连接支持模块 |
| ngx_slab_stat | 该模块可以提供 Nginx/Tengine 共享内存的状态信息 |

上述模块功能说明来源于源码中的说明文档，具体使用方法可参照源码中的说明文档。

Tengine 编译完成后，可使用 `nginx -m` 命令查看所有已经加载的模块，static 标识是静态编译的，shared 标识是动态编译的。