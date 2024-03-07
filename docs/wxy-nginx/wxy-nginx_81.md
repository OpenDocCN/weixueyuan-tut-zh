# Nginx 集群配置管理实例

> 原文：[`www.weixueyuan.net/a/840.html`](http://www.weixueyuan.net/a/840.html)

根据部署规划，如果对 Nginx 集群配置实现管理，需要在 GitLab、Jenkins 上完成相关的配置及编写 Ansible 剧本。本节将通过对配置文件 nginx.conf 举例 GitLab、Jenkins 及 Ansible 的配置，以实现 Nginx 配置管理的操作。

## 1、GitLab 配置

首先为 Nginx 配置创建用户及 Nginx 项目，操作步骤如下：

*   创建发布用户 gitlab_nginx：Admin Area → Users → New User 用户名 gitlab_nginx。
*   创建项目组 nginx：GitLab 登录后创建项目组（Group）nginx，可视级别（Visibility Level）为 Private。
*   添加组用户：将用户 gitlab_nginx 添加到项目组 nginx 中，权限为 Developer。
*   创建 Nginx 配置项目 homebox：按照 Nginx 集群名称创建 Gitlab 项目，nginx 组项目 → New project，命名为 home-box。
*   初始化：进入 Nginx 配置文件目录，将配置文件初始化到 GitLab 仓库中。初始化命令如下：

git init
git remote add origin http://IP:8080/nginx/homebox.git
git add .
git commit -m "Initial commit"
git push -u origin master

## 2、Ansible 剧本

根据 Nginx 配置目录的规划，定义 Ansible 剧本目录结构如下：

.
├── ansible.cfg
├── hosts
└── roles
    ├── nginx
    │   ├── defaults
    │   │   └── main.yaml
    │   ├── files
    │   │   ├── gzip.conf
    │   │   ├── fscgi.conf
    │   │   └── proxy.conf
    │   ├── handlers
    │   │   └── main.yaml
    │   ├── tasks
    │   │   ├── config_nginx.yaml
    │   │   ├── config_server.yaml
    │   │   ├── config_status.yaml
    │   │   ├── deploy.yaml
    │   │   ├── install.yaml
    │   │   ├── rollback.yaml
    │   │   └── main.yaml
    │   └── templates
    │       ├── nginx.conf
    │       ├── server.conf
    │       └── status.conf
    └── nginx.yaml

关于目录的说明如下所示：

*   defaults 目录中的 main.yaml 是自定义默认变量值的描述文件，文件内容如下：

```

self_services: nginx
exclude: ".git"
rsync_opts:
    - "--exclude={{ exclude }}"
process_events: >
    worker_processes auto;
    worker_rlimit_nofile 65535;
    worker_priority -5;
modules: ""
server: ""
confdir: "/etc/nginx"
env_packages:
    - pcre-devel
    - zlib-devel
    - openssl-devel
    - libxml2-devel
    - libxslt-devel
    - gd-devel
    - GeoIP-devel
    - jemalloc-devel
    - libatomic_ops-devel
    - luajit
    - luajit-devel
    - perl-devel
    - perl-ExtUtils-Embed
```

*   gzip.conf、fscgi.conf、proxy.conf 这 3 个文件是全局的配置文件，放在 files 目录中仅作 Nginx 初始化安装时使用。
*   handlers 的 main.yaml 是处理器任务描述文件，文件内容如下：

```

# 重启 Nginx 服务任务
- name: Restart Nginx services
  service:
    name: "{{ self_services }}"
    state: restarted

# 启动 Nginx 服务任务
- name: Start Nginx services
  service:
    name: "{{ self_services }}"
    state: started
```

*   tasks 目录中的 main.yaml 是当前角色的默认入口文件，文件内容如下：

```

---
    # 当变量 deploy 的值为 deploy 时执行 deploy.yaml 的任务步骤
    - name: "Starting deploy for nginx"
      include_tasks: deploy.yaml
      when: deploy == "deploy"

    # 当变量 deploy 的值为 rollback 时执行 rollback.yaml 的任务步骤
    - name: "Starting rollback for nginx"
      include_tasks: rollback.yaml
      when: deploy == "rollback"
```

*   tasks 目录中的 deploy.yaml 是修改配置的任务分支描述文件，文件内容如下：

```

---
        # 检查目标服务器是否存在配置文件，并将检查结果赋值给变量 has_nginx
    - name: "check nginx service"
      stat: path={{ confdir }}/nginx.conf
      register: has_nginx

        # 如果目标服务器不存在 Nginx 服务器则调用分支任务 install 进行安装
    - name: "Starting install nginx "
      include_tasks: install.yaml
      when: not has_nginx.stat.exists

        # 如果当前任务为配置 nginx.conf，则调用 config_nginx 任务配置 nginx.conf 文件
    - name: "Starting config nginx.conf "
      include_tasks: config_nginx.yaml
      when: not jobname == "" and jobname == "nginx.conf"

        # 如果当前任务为配置 status.conf，则调用 config_status 任务配置 status.conf 文件
    - name: "Starting config website status for nginx"
      include_tasks: config_status.yaml
      when: not jobname == "" and jobname == "status.conf"

        # 如果当前任务为配置 server.conf，则调用 config_server 任务配置 server.conf 文件
    - name: "Starting config website server for nginx"
      include_tasks: config_server.yaml
      when: not jobname == "" and jobname == "server.conf"

        # 初始化 rsync 模块的 ssh 免登录 key
    - name: add authorized_keys
      authorized_key:
          user: "{{ ansible_user_id }}"
          key: "{{ lookup('file', '/home/jenkins/.ssh/id_rsa.pub') }}"
          state: present
          exclusive: no

        # 使用 rsync 模块将 Nginx 配置文件同步到目标机器
    - name: check rsync_opts rsync dir
      synchronize:
          src: "{{ work }}/"
          dest: "{{ confdir }}"
          delete: yes
          copy_links: yes
          private_key: "/home/jenkins/.ssh/id_rsa"
          rsync_opts: "{{ rsync_opts }}"
        register: rsync_result

        # 输出 rsync 的执行详情
    - debug: msg="{{ rsync_result.stdout_lines }}"

        # 使用 Nginx 的测试参数测试配置文件是否存在语法错误
    - name: Test Nginx Config
      shell: nginx -c {{ confdir }}/nginx.conf -t -q
      ignore_errors: True
      register: test_result

        # 如果执行检测失败，则停止当前任务，并输出检测结果
    - fail: msg="{{ test_result.stderr_lines }}"
      when: test_result.failed

        # 热加载 Nginx 进程
    - name: reload Nginx Service
      systemd: "name=nginx state=reloaded enabled=yes"
```

*   tasks 目录中的 rollback.yaml 是回滚配置的任务分支描述文件，文件内容如下：

```

---
    - name: check rsync_opts rsync dir
      synchronize:
          src: "{{ work }}/"
          dest: "{{ confdir }}"
          delete: yes
          copy_links: yes
          private_key: "/home/jenkins/.ssh/id_rsa"
          rsync_opts: "{{ rsync_opts }}"
        register: rsync_result

    - debug: msg=" {{ rsync_result.stdout_lines }} "

    - name: "Test Nginx Config"
      shell: nginx -c {{ confdir }}/nginx.conf -t -q
      ignore_errors: True
      register: test_result

    - fail: msg="{{ test_result.stderr_lines }}"
      when: test_result.failed

    - name: reload Nginx Service
      systemd: "name=nginx state=reloaded enabled=yes"
      register: test_result
```

*   tasks 目录中的 config_nginx.yaml 是 Nginx 配置文件 nginx.conf 的任务分支描述文件，文件内容如下：

```

---
        # 通过模板文件与外部输入变量生成新的 nginx.conf 文件，替换 Jenkins 的工作目录中的
        # nginx.conf
    - name: "Starting init nginx.conf "
      template: src=nginx.conf dest={{ work }}/nginx.conf
      delegate_to: localhost

        # 因外部参数中的单、双引号及变量符号被转义，此处则重新替换回原符号
    - name: "Starting format nginx.conf "
      shell: sed -i 's/%24/$/g' {{ work }}/nginx.conf && sed -i 's/%9c/\"/g' {{ work }}/nginx.conf && sed -i "s/%98/\'/g" {{ work }}/nginx.conf && python /etc/ansible/bin/nginxfmt.py {{ work }}/nginx.conf
      delegate_to: localhost
```

*   tasks 目录中的 config_server.yaml 是 Nginx 配置文件中配置各虚拟主机的任务分支描述文件，文件内容如下：

```

---
        # 通过模板文件与外部输入变量生成新的虚拟主机文件，替换 Jenkins 的工作目录中虚拟主机
        # 文件并在 conf.d 目录下保存
    - name: "Starting init {{ jobname }} "
      template: src=server.conf dest={{ workdir }}/conf.d/{{ jobname }}.conf
      delegate_to: localhost

        # 因外部参数中的单、双引号及变量符号被转义，此处则重新替换回原符号
    - name: "Starting format nginx.conf "
      shell: sed -i 's/%24/$/g' {{ work }}/nginx.conf && sed -i 's/%9c/\"/g' {{ work }}/nginx.conf && sed -i "s/%98/\'/g" {{ work }}/nginx.conf && python /etc/ansible/bin/nginxfmt.py {{ work }}/nginx.conf
      delegate_to: localhost
```

*   tasks 目录中的 config_staus.yaml 是 Nginx 配置文件中统一状态监控的虚拟主机任务描述文件，文件内容如下：

```

---
        # 通过模板文件与外部输入变量生成新的状态监控虚拟主机文件，替换 Jenkins 的工作目录中
        # 的 conf.d 目录下保存
    - name: "Starting init status.conf "
      template: src=status.conf dest={{ workdir }}/conf.d/status.conf
      delegate_to: localhost

      # 因外部参数中的单、双引号及变量符号被转义，此处则重新替换回原符号
    - name: "Starting format nginx.conf "
      shell: sed -i 's/%24/$/g' {{ work }}/nginx.conf && sed -i 's/%9c/\"/g' {{ work }}/nginx.conf && sed -i "s/%98/\'/g" {{ work }}/nginx.conf && python /etc/ansible/bin/nginxfmt.py {{ work }}/nginx.conf
      delegate_to: localhost
```

*   tasks 目录中的 install.yaml 是 Nginx 的部署任务描述文件，文件内容如下：

```

    # 添加 Nginx yum 安装源
    - name: add repo
      yum_repository:
          name: nginx
          description: nginx repo
          baseurl: http://nginx.org/packages/centos/7/$basearch/
          gpgcheck: no
          enabled: 1
    # 安装环境依赖包
    - name: install centos packages
      yum:
          name: "{{ env_packages }}"
          disable_gpg_check: yes
          state: present
    # yum 方式安装 Nginx，并触发处理器 Start Nginx services 任务
    - name: install nginx
      yum:
          name: nginx
          state: latest
      notify: Start Nginx services
```

*   templates 目录中的 nginx.conf 为配置文件 nginx.conf 的模板文件，文件内容如下：

```

{{ modules }}
{{ process_events }}
stream {
    {{ stream }}
    include conf.d/*.ream;
}
http {
    {{ http }}

    {% if gzip != "false" %}
    include gzip.conf;                      # HTTP gzip 的配置文件
    {% endif %}

    {% if fscgi != "false" %}
    include fscgi.conf;                     # FastCGI 代理的配置文件
    {% endif %}

    {% if proxy != "false" %}
    include proxy.conf;                     # HTTP 代理配置
    {% endif %}

    include conf.d/*.conf;
}
```

*   templates 目录中的 server.conf 为配置文件中虚拟服务器的模板文件，文件内容如下：

```

{{ global }}
upstream {
{{ upstream }}
}
server{
{{ server }}
}
```

*   templates 目录中的 status.conf 为配置文件中用于状态监控的虚拟主机模板文件，文件内容如下：

```

{{ global }}
server{
{{ server }}
}
```

*   roles 目录中的 nginx.yaml 为主剧本文件，该剧本文件调用了角色 Nginx，使用外部变量、应用角色 Nginx 中的任务描述文件完成 Nginx 的配置修改、同步及加载动作，文件内容如下：

```

---
# 变量 hosts 由外部输入，设定操作的目标主机
- hosts:
        - "{{ hosts }}"
    max_fail_percentage: 30 # 当有 30%的操作目标任务执行出错时，则终止整个剧本的执行
    serial: "{{ serial }}"  # 该模块可以设定操作目标数量实现灰度发布的效果，当设定为
                                        # 30%且操作目标为 3 台时，则表示一次仅操作一个目标
    roles:
        - nginx                         # 调用 Nginx 角色
```

## 3、Jenkins 配置

根据 GitLab 及 Ansible 剧本的设置，Jenkins 需要创建具有如下操作内容的任务：

*   通过 Web 页面设定 Nginx 的配置内容；
*   使用账号 gitlab_nginx 从 GitLab 中获取 Nginx 的配置文件；
*   调用 Ansible 剧本实现 Nginx 配置文件中的修改、同步及加载；
*   实现修改文件的归档；
*   实现修改内容的快速回滚；
*   对操作者设定访问的权限；
*   对发布的历史可以查看；
*   可以满足多个 Nginx 集群的配置管理。

按照上述需求的设定，可以将不同的 Nginx 集群以文件夹类型任务进行创建，每个 Nginx 集群文件夹中包括 nginx.conf、status.conf 全局配置的自由风格任务，每个虚拟主机则按照虚拟主机名称创建自由风格任务分列在该集群文件夹下。任务层级结构如下：

```

homebox                     # Nginx 集群名称，任务类型为文件夹
    nginx.conf              # nginx.conf 任务，任务类型为自由风格
    status.conf             # status.conf 任务，任务类型为自由风格
    www.nginxbar.org        # 虚拟主机任务，任务类型为自由风格
```

该任务层级设计，可以使操作者清晰地知道所操作的 Nginx 集群，同时还可以结合 Jenkins 的权限功能进行细粒度的权限控制。任务配置 nginx.conf 的创建步骤首先是在全局配置阶段通过参数化构建插件实现 Web 化变量的输入，通过参数化配置，设计部署与回滚操作选项。当选择回滚时，通过 Git 参数插件提供 Git 标签（tag）筛选功能列出可用的 Git 标签，选择后执行回滚操作。同时还要按照之前的规划在此阶段将 nginx.conf 文件内容分割成多个不同的变量，并定义为构建参数，让发布者在点击参数化构建后，可以通过 Web 界面进行选择和修改。

在构建操作配置阶段，编写 shell 脚本对所有输入的变量进行判断、修整后通过 ansible-playbook 命令传递给 Nginx 剧本，完成 Nginx 配置的修改、同步及加载操作。若在构建后动作配置阶段，则通过 Git Publisher 插件将当前的修改标记 Git 标签进行归档。详细配置过程如下。

#### 1) 全局配置

*   定义时间戳变量格式 [Change date pattern for the BUILD_TIMESTAMP（build time-stamp）variable] 为 yyyyMMdd
*   选项参数 deploy，选项（Choices）为 deploy 和 rollback，用以定义构建脚本中的变量，进行控制是更新配置文件还是回滚以前的配置操作。
*   Git 参数（Git Parameter）tag，参数类型为 tag，过滤（Tag Filter）配置为 nginx.conf-deploy-*，排序（Sort Mode）选择 DESCENDING，默认值为 Default Value。该参数可以获取当前任务 Git 仓库的分支及 tag 列表，这里获取过滤被标记为部署成功的 Git 标签，用以实现代码回滚。
*   文本参数 modules，加载动态模块，参数值如下：

```

# 选择加载动态模块
load_module "modules/ngx_http_geoip_module.so";
load_module "modules/ngx_http_image_filter_module.so";
load_module "modules/ngx_http_xslt_filter_module.so";
```

*   文本参数 process_events，工作进程及事件配置，参数值如下：

```

# 工作进程及事件配置，定义文本参数 process_events
worker_processes auto;                  # 启动与 CPU 核数一致的工作进程
worker_priority -5;                     # 工作进程在 Linux 系统中的优先级为-5

events {
    worker_connections  65535;              # 每个工作进程的最大连接数
    multi_accept on;                        # 每个工作进程每次都可以接受多个连接
}
```

*   文本参数 stream，加载 TCP/UDP 代理配置，参数值如下：

```

# 配置 TCP/UDP 代理的日志格式模板，模板名为 tcp
log_format  tcp  '$remote_addr - $connection - [$time_local] $server_addr:$server_port - $protocol'
                    '- $status - $upstream_addr - $bytes_received - $bytes_sent - $session_time '
                    '- $proxy_protocol_addr:$proxy_protocol_port ';

# 配置 TCP/UDP 代理的错误日志输出位置，错误级别为 error
error_log /var/log/nginx/tcp_error.log error;
```

*   文本参数 http，加载 HTTP 配置，参数值如下：

```

include       mime.types;               # 引入 MIME 类型映射表文件

# 配置 HTTP 的错误日志输出位置，错误级别为 error
error_log /var/log/nginx/error.log error;

# 配置 HTTP 的日志格式，模板名为 main
log_format  main  '$remote_addr - $connection - $remote_user [$time_local] "$request" - $upstream_addr '
                 '$status  - $body_bytes_sent - $request_time - "$http_referer" '
                 '"$http_user_agent" - "$http_x_forwarded_for" - ';

# 配置全局访问日志输出位置，并使用模板 main 的日志格式输出
access_log  /var/log/nginx/access.log  main;

charset  utf-8;                         # 字符编码为 utf-8
variables_hash_max_size 2048;           # 变量哈希表最大值为 2048 字节
variables_hash_bucket_size 128;         # 变量哈希桶最大值为 128 字节
server_names_hash_bucket_size 256;      # 服务主机名哈希桶大小为 256 字节
client_header_buffer_size 32k;          # 请求头缓冲区的大小为 32KB
large_client_header_buffers 4 128k;     # 最大缓存为 4 个 128KB
client_max_body_size 20m;               # 允许客户端请求的最大单个文件字节数为 20MB
sendfile on;                            # 开启零复制机制
tcp_nopush on;                          # 启用在零复制时数据包最小传输的限制机制
tcp_nodelay on;                         # 当处于保持连接状态时，以最快方式发送数据包
keepalive_timeout  60;                  # 保持连接超时时间为 60s
client_header_timeout 10;               # 读取客户请求头的超时时间是 10s
client_body_timeout 10;                 # 请求体接收超时时间为 10s
server_tokens on;                       # 不显示 Nginx 版本信息
```

*   布尔值参数 proxy，该设置默认为选中，用以选择是否加载代理相关指令配置。
*   布尔值参数 gzip，该设置默认为不选中，用以选择是否加载 gzip 相关指令配置。
*   布尔值参数 fscgi，该设置默认为不选中，用以选择是否加载 FastCGI 相关指令配置。

#### 2) 代码仓库配置

*   添加 GitLab 的地址、账户及密码。
*   构建分支（Branches to build），填写 ${tag}，Git 参数定义的变量。

#### 3) 构建环境配置

*   选择构建前先删除之前的构建目录（Delete workspace before build starts）。
*   选择设置 Jenkins 用户变量（Set jenkins user build variables）。

#### 4) 构建操作配置

编写构建脚本。

```

#!/bin/bash
set -x

# 初始化变量
jobname=${JOB_NAME}
jobnum=${BUILD_TIMESTAMP}-${BUILD_NUMBER}
OLD_IFS="$IFS" ;IFS="/" ;arr=($jobname) ;IFS="$OLD_IFS"
cluster=${arr[1]}
name=${arr[2]}

# 部署时执行的操作
if [ "$deploy" == "deploy" ];then

    rm -rf *.default

    # 对变量中的单引号、双引号及变量符号进行转义
    stream=${stream//$/%24}
    stream=${stream//\'/%98}
    stream=${stream//\"/%9c}

    http=${http//$/%24}
    http=${http//\'/%98}
    http=${http//\"/%9c}

    # 生成当前配置变量
    jobvars="process_events='$process_events' modules='$modules' stream= '$stream' http='$http' proxy='$proxy' gzip='$gzip' fscgi='$fscgi'"

fi

# 回滚时执行的操作
if [ "$deploy" == "rollback" ];then
    OLD_IFS="$IFS" ;IFS="-" ;arr=($tag) ;IFS="$OLD_IFS"
    jobnum=${arr[${#arr[@]}-2]}-${arr[${#arr[@]}-1]}
    jobvars=""
fi

# 生成版本信息
echo  "#$cluster-$name-$jobnum $deploy by ${BUILD_USER}"  >version.txt

# 生成任务变量
vars="hosts=$cluster jobname=$name  work=${WORKSPACE} serial=30% deploy= '$deploy' $jobvars "

# 执行 Ansible 剧本
ansible-playbook -i /etc/ansible/hosts /etc/ansible/roles/nginx.yaml --extra-vars "$vars "
if [ $? -ne 0 ];then exit 1; fi

# 执行部署操作成功时，对变更的配置文件进行归档
if [ "$deploy" == "deploy" ];then
    git add .
    git commit -m "#$cluster-$name-$jobnum deploy by ${BUILD_USER}"
fi
```

添加修改构建名（Update build name），选择从文件名中读取（Read from file），文件名填写为 version.txt。

#### 5) 构建后动作配置

*   使用 Git Publisher 插件，将修改成功的代码提交到 Gitlab 中，并打标签（tag）为当前构建的时间戳和编号；
*   选择构建成功后，再打标签（Push Only If Build Succeeds）；
*   选择合并结果（Merge Results）；
*   标签名（Tag to push），填写$ {JOB_NAME}-${deploy}-${BUILD_TIMESTAMP}-${BUILD_NUMBER}；
*   选择创建新标签（Create new tag）。

配置文件 staus.conf 及 server.conf 的 Jenkins 任务创建过程仅与 nginx.conf 在构建的参数配置和 shell 脚本上略有变化，此处就不一一详细举例了。

Jenkins 拥有诸多功能强大的插件，使其可以完成各种部署及发布的操作需求。例如，可以通过 jQuery 插件对 Jenkins 的操作界面进行自定义修改，增加根据选择项动态实现参数选项的显示和隐藏，或者增加自定义按钮实现配置预览等功能，此处就不再进行深入探讨了。结合 GitLab、Ansible 及 Jenkins 等开源软件，用户可以根据实际需求，不断优化并打造符合自身需求的 Nginx 配置管理工具。