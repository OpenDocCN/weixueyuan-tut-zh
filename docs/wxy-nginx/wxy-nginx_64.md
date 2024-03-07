# Nginx 动态更新 upstream

> 原文：[`www.weixueyuan.net/a/786.html`](http://www.weixueyuan.net/a/786.html)

Nginx 的配置是启动时一次性加载到内存中的，在实际的使用中，对 Nginx 服务器上游服务器组中节点的添加或移除仍需要重启或热加载 Nginx 进程。在 Nginx 的商业版本中，提供了 ngx_http_api_module 模块，可以通过 API 动态添加或移除上游服务器组中的节点。

对于 Nginx 开源版本，通过 Nginx 的扩展版 OpenResty 及 Lua 脚本也可以实现上游服务器组中节点的动态操作，这里只使用 OpenResty 的 lua-upstream-nginx-module 模块简单演示节点的上下线状态动态修改的操作。该模块提供了 set_peer_down 指令，该指令可以对 upstream 的节点实现上下线的控制。

由于该指令只支持 worker 级别的操作，为使得 Nginx 的所有 worker 都生效，此处通过编写 Lua 脚本与 lua-resty-upstream-healthcheck 模块做了简单的集成，利用 lua-resty-upstream-healthcheck 模块的共享内存机制将节点状态同步给其他工作进程，实现对 upstream 的节点状态的控制。

首先在 OpenResty 的 lualib 目录下创建公用函数文件 api_func.lua，lualib/api_func.lua 内容如下：

```

local _M = { _VERSION = '1.0' }
local cjson = require "cjson"
local upstream = require "ngx.upstream"
local get_servers = upstream.get_servers
local get_primary_peers = upstream.get_primary_peers
local set_peer_down = upstream.set_peer_down

# 分割字符串为 table
local function split( str,reps )
    local resultStrList = {}
    string.gsub(str,"[^"..reps.."]+",function ( w )
        table.insert(resultStrList,w)
    end)
    return resultStrList
end

# 获取 server 列表
local function get_args_srv( args )
    if not args["server"] then
        ngx.say("failed to get post args: ", err)
        return nil
    else
        if type(args["server"]) ~= "table" then
            server_list=split(args["server"],",")
        else
            server_list=args["server"]
        end
    end
    return server_list
end

# 获取节点在 upstream 中的顺序
local function get_peer_id(ups,server_name)
    local srvs = get_servers(ups)
    for i, srv in ipairs(srvs) do
        -- ngx.print(srv["name"])
        if srv["name"] == server_name then
            target_srv = srv
            target_srv["id"] = i-1
            break
        end
    end
    return target_srv["id"]
end

# 获取节点共享内存 key
local function gen_peer_key(prefix, u, is_backup, id)
    if is_backup then
        return prefix .. u .. ":b" .. id
    end
    return prefix .. u .. ":p" .. id
end

# 设置节点状态
local function set_peer_down_globally(ups, is_backup, id, value,zone_define)
    local u = ups
    local dict = zone_define
    local ok, err = set_peer_down(u, is_backup, id, value)
    if not ok then
        ngx.say(cjson.encode({code = "E002", msg = "failed to set peer down", data = err}))
    end

    local key = gen_peer_key("d:", u, is_backup, id)
    local ok, err = dict:set(key, value)
    if not ok then
        ngx.say(cjson.encode({code = "E003", msg = "failed to set peer down state", data = err}))
    end
end

# 获取指定 upstream 的节点列表
function  _M.list_server(ups)
    local srvs, err = get_servers(ups)
    ngx.say(cjson.encode(srvs))
end

# 设置节点状态
function  _M.set_server(ups,args,status,backup,zone_define)
    local server_list = get_args_srv(args)
    if server_list == nil then
        ngx.say(cjson.encode({code = "E001", msg = "no args",data = server_list}))
        return nil
    end

    for _, s in pairs(server_list) do
        local peer_id = get_peer_id(ups,s)
        if status then
            local key = gen_peer_key("nok:", ups, backup, peer_id)
            local ok, err = zone_define:set(key, 1)
            set_peer_down_globally(ups, backup, peer_id, true,zone_define)
        else
            local key = gen_peer_key("ok:", ups, backup, peer_id)
            local ok, err = zone_define:set(key, 0)
            set_peer_down_globally(ups, backup, peer_id, nil,zone_define)
        end
    end
    ngx.say(cjson.encode({code = "D002", msg = "set peer is success",data = server_list}))
end

return _M
```

Nginx 配置文件 status.conf 的内容如下：

```

# 关闭 socket 错误日志
lua_socket_log_errors off;

# 设置共享内存名称及大小
lua_shared_dict _healthcheck_zone 10m;

init_worker_by_lua_block {
    local hc = require "resty.upstream.healthcheck"

    # 设置需要健康监测的 upstream
    local ups = {"foo.com","sslback"}

    # 遍历 ups，绑定健康监测策略
    for k, v in pairs(ups) do
        local ok, err = hc.spawn_checker{
            shm = "_healthcheck_zone",      # 绑定 lua_shared_dict 定义的共享内存
            upstream = v,                   # 绑定 upstream 指令域
            type = "http",
            http_req = "GET / HTTP/1.0\r\nHost: foo.com\r\n\r\n",
                                            # 用以检测的 raw 格式 http 请求

            interval = 2000,                # 每 2s 检测一次
            timeout = 1000,                 # 检测请求超时时间为 1s
            fall = 3,                       # 连续失败 3 次，被检测节点被置为 DOWN 状态
            rise = 2,                       # 连续成功 2 次，被检测节点被置为 UP 状态
                                            # 当健康检测请求返回的响应码为 200 或 302 时，被认
                                            # 为检测通过
            valid_statuses = {200, 302},
            concurrency = 10,               # 健康检测请求的并发数为 10
        }
        if not ok then
            ngx.log(ngx.ERR, "failed to spawn health checker: ", err)
            return
        end
    end
}

upstream foo.com {
    server 192.168.2.145:8080;
    server 192.168.2.109:8080;
    server 127.0.0.1:12356 backup;
}

upstream sslback {
    server 192.168.2.145:443;
    server 192.168.2.159:443;
}

server {
    listen 18080;
    access_log  off;
    error_log off;

    # 健康检测状态页
    location = /healthcheck {
        access_log off;
        allow 127.0.0.1;
        allow 192.168.2.0/24;
        allow 192.168.101.0/24;
        deny all;

        default_type text/plain;
        content_by_lua_block {
            local hc = require "resty.upstream.healthcheck"
            ngx.say("Nginx Worker PID: ", ngx.worker.pid())
            ngx.print(hc.status_page())
        }
    }

    location = /ups_api {
        default_type  application/json;
        content_by_lua '
            # 获取 URL 参数
            local ups = ngx.req.get_uri_args()["ups"]
            local act = ngx.req.get_uri_args()["act"]
            if act == nil or ups == nil then
                ngx.say("usage: /ups_api?ups={name}&act=[down,up,list]")
                return
            end

            # 引用 api_func.lua 脚本
            local api_fun = require "api_func"
            # 绑定共享内存 _healthcheck_zone
            local zone_define=ngx.shared["_healthcheck_zone"]

            if act == "list" then
                # 获取指定 upstream 的节点列表
                api_fun.list_server(ups)
            else
                ngx.req.read_body()
                local args, err = ngx.req.get_post_args()
                if act == "up" then
                    # 节点状态将设置为 UP
                    api_fun.set_server(ups,args,false,false,zone_define)
                end
                if act == "down" then
                    # 节点状态将设置为 DOWN
                    api_fun.set_server(ups,args,true,false,zone_define)
                end
            end
        ';
    }
}
```

操作命令如下：

# 查看 upstream foo.com 的服务器列表
curl "http://127.0.0.1:18080/ups_api?act=list&ups=foo.com"

# 将 192.168.2.145:8080 这个节点设置为 DOWN 状态
curl -X POST -d "server=192.168.2.145:8080" "http://127.0.0.1:18080/ups_api?act= down&ups=foo.com"

# 将 192.168.2.145:8080 这个节点设置为 UP 状态
curl -X POST -d "server=192.168.2.145:8080" "http://127.0.0.1:18080/ups_api?act= up&ups=foo.com"