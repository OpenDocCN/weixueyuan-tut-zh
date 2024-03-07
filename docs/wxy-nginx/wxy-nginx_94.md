# Nginx Kong（微服务网关）应用实例

> 原文：[`www.weixueyuan.net/a/888.html`](http://www.weixueyuan.net/a/888.html)

作为一款微服务网关应用，Kong 通过插件功能实现了微服务网关的多种功能，此处分别以访问认证、请求终止、数据整形为例，为了方便读者理解和应用，此处均使用管理接口直接操作，功能参数仍以 Konga 页面显示的名称进行说明。

## 1、访问认证

Kong 提供了基本认证、密钥认证、OAuth2 认证、HMAC 认证、JWT 认证、LDAP 认证等多种方式的认证插件，此处列举常见的密钥认证方式配置。密钥认证插件参数说明如下表所示。

| 参数名 | 参数说明 |
| consumer | 消费者 ID，如果为空则对所有消费者有效 |
| key names | 密钥名称，用户可在请求头或请求参数中使用该字段提交密钥，该值为数组，可以添加多个名称。密钥名只能包含 [a-2]1a-2][0-91[uu] 和 [-] |
| hide credentials | 设置是否将密钥名称字段传递给被代理服务器，指令值为 true 时将不向被代理服务器传递该字段 |
| anonymous | 如果身份验证失败，则用作 anonymous 消费者的可选字符串（消费者对象实例 uuid）值。默认值为空，表示直接返回验证失败状态码 4××。此值必须引用 Kong 内的消费者 ID 属性，而不是 "custom id" |
| key in body | 如果设置为 true，Kong 插件将尝试从请求体中读取密钥名称，支持请求体的 MIME 类型有 application/www-form-urlencoded、application/json 和 multipart/form-data |
| run on preflight | 如果设置为 true，该插件将在请求之前运行。否则在请求任何阶段均被允许 |

接口认证是服务开发中常见的功能，Kong 插件的认证功能可以让开发工程师不必单独开发此功能，仅需选择使用 Kong  的认证机制或通过认证转发使用内部的认证服务器，让所有的接口服务很容易地实现统一认证的功能。在下面的配置样例中，在 Konga 中按照参数配置添加密钥认证插件，认证密钥名称为 apikey。

```

# 创建服务
curl -i -X POST \
--url http://10.10.4.8:8001/services/ \
--data 'name=baidu' \
--data 'url=https://www.baidu.com'

# 创建路由
curl -i -X POST \
--url http://10.10.4.8:8001/services/baidu/routes \
--data 'name=baidu' \
--data 'paths[]=/v1/baidu'

# 访问测试，确认路由规则
curl -i -X GET \
    --url http://10.10.4.8:8000/v1/baidu

# 关联插件到路由对象实例 baidu
curl -i -X POST \
    --url http://10.10.4.8:8001/routes/baidu/plugins \
    --data "name=key-auth" \
    --data "config.key_names=apikey" \

# 创建消费者
curl -d "username=test123" http://10.10.4.8:8001/consumers/

# 创建消费者密钥
curl -X POST http://10.10.4.8:8001/consumers/test123/key-auth -d ''

# 查看并获得密钥
curl http://10.10.4.8:8001/consumers/test123/key-auth

# 消费者使用密钥访问
curl -i -X GET \
    --url http://10.10.4.8:8000 \
    --header "apikey: xKgpAM6qBQE3e8nrR51dIrK89ggRdelf"
```

## 2、请求终止

请求终止（request-termination）插件原设计场景是进行请求熔断等安全管理，但其同样适用于做依赖该接口的测试桩场景，通过 Kong 的请求终止插件可以非常快速地实现该功能，而且不需要做任何代码改动，测试桩的创建和撤销也非常简单。插件参数说明如下表所示。

| 参数名  | 参数说明 |
| consumer | 消费者 ID，如果为空则对所有消费者有效 |
| status code | 返回响应状态码 |
| content type | 相应数据 MIEM 类型 |
| body | 设置响应数据内容 |

下面是一个测试桩的样例，该插件可以对当前接口的请求返回固定格式的 JSON 数据，该场景可以满足不同团队合作时在真实业务 API 代码开发完毕前，让合作方、前端及测试人员进行代码升级或测试。

```

# 创建服务
curl -i -X POST \
--url http://10.10.4.8:8001/services/ \
--data 'name=baidu' \
--data 'url=https://www.baidu.com'

# 创建路由
curl -i -X POST \
--url http://10.10.4.8:8001/services/baidu/routes \
--data 'name=baidu2' \
--data 'paths[]=/v2/baidu'

# 访问测试，确认路由规则
curl -i -X GET \
    --url http://10.10.4.8:8000/v2/baidu

# 关联插件到路由对象实例 baidu
curl -i -X POST \
    --url http://10.10.4.8:8001/routes/baidu2/plugins \
    --data "name=request-termination" \
    --data "config.status_code=200" \
    --data "config.content_type=application/json; charset=utf-8" \
    --data "config.body={\"status\": 200, \"data\": {\"status_code\": 403, \"message\": \"测试数据\"}, \"message\": \"专业测试桩\"}"

# 测试结果
curl -i -X GET \
    --url http://10.10.4.8:8000/v2/baidu
```

## 3、数据整形

通常一套服务提供的 JSON 格式数据是固定的，但在多个团队的开发合作中，可能需要对接口数据返回格式有不同的需求，以往大家都希望用一个统一的标准进行规范化的 JSON 数据格式输出，但执行起来则会遇到诸多现实问题。

通过 Kong 的插件可以让使用方和供给方不必再为这种标准而纠结，开发人员不需要修改代码，仅需要简单进行 Lua 脚本编写就可以实现现有服务的供给或使用需求，这里使用 Kong 的第三方插件 API 转换（API Transformer）插件做样例在中间进行数据整形，大家也可以根据实际需求定制自己的 Kong 插件。API 转换插件功能参数如下表所示。

| 参数名 | 参数说明 |
| consumer | 消费者 ID，如果为空则对所有消费者有效 |
| request transformer | 请求数据整形脚本路径，必选项 |
| response transformer | 响应数据整形脚本路径，必选项 |
| http 200 always | HTTP 响应码总为 200 |

此处演示将管理接口返回的 JSON 数据格式修改为前端 jQuery 插件 DataTables 的数据格式。因 API 转换插件的 request_transformer 参数为必选项，即便不需要请求阶段数据整形，也要为此参数指定文件，如下样例中将创建一个返回空数据的 req.lua 文件。

```

# 安装插件
git clone https://github.com/qnap-dev/kong-plugin-api-transformer.git
cd kong-plugin-api-transformer
luarocks make

# 启用插件，需要重启 Kong 才可生效
sed -i "/\"session\",/a\  \"api-transformer\"" /usr/local/share/lua/5.1/kong/constants.lua

# 创建服务，代理目标为管理接口
curl -i -X POST \
--url http://10.10.4.8:8001/services/ \
--data 'name=adminapi' \
--data 'url=http://10.10.4.8:8001'

# 创建路由
curl -i -X POST \
--url http://10.10.4.8:8001/services/adminapi/routes \
--data 'name=adminapi' \
--data 'paths[]=/adminapi'

# 访问测试，确认路由策略
curl -i -X GET \
    --url http://10.10.4.8:8000/adminapi

# 关联插件到路由 adminapi
curl -X POST http://10.10.4.8:8001/routes/adminapi/plugins \
    --data "name=api-transformer"  \
    --data "config.request_transformer=/etc/kong/scripts/req.lua" \
    --data "config.response_transformer=/etc/kong/scripts/datatables.lua" \
    --data "config.http_200_always=true"

# 创建 req.lua，此处需要在 Kong 系统中进行操作
mkdir -p /etc/kong/scripts
echo "return true, \"\"" > /etc/kong/scripts/req.lua

# 创建响应数据整形脚本 datatables.lua，此处需要在 Kong 系统中进行操作
cat>/etc/kong/scripts/datatables.lua<<EOF
local return_body = {
    data = {}
}
local _resp_json_body = ngx.ctx.resp_json_body
return_body.data = _resp_json_body.data
local i = 0;
for _, obj in pairs(return_body.data) do
    # 此处可进行相关字段的变更或过滤
    i = i + 1;
end
return_body["page_size"] = i
return_body["recordsFiltered"] = i
return_body["recordsTotal"] = i
return true, _cjson_encode_(return_body)
EOF

# 访问测试，确认返回数据
curl -i -X GET \
    --url http://10.10.4.8:8000/adminapi/routes
```

Kong 的插件都是基于 Lua 脚本实现的，通过 Nginx 可以实现用户请求过程中各阶段的数据操作，此处不再举例，大家可以根据实际需求灵活使用 Kong 的功能。