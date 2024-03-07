# Nginx Python（Django）项目部署

> 原文：[`www.weixueyuan.net/a/727.html`](http://www.weixueyuan.net/a/727.html)

CentOS 7 系统默认安装 Python 2.7 版本，本节搭建的是基于 Python3 的 Django 网站，所以需要升级到 Python3 版本。

## 1、安装 Python 及 Django

配置样例如下：

```

yum install -y epel-release              # 安装 EPEL 扩展源
yum install -y python36 python36-pip python36-devel \
                sqlite-devel supervisor  # 安装 Python3.6 及其工具组件
ln -s /usr/bin/pip3 /usr/bin/pip         # 设置 pip3 为默认 pip
pip install --upgrade pip                # 升级 pip 版本
echo "alias python='/usr/bin/python3.6'" >/etc/profile.d/python.sh
                                                # 添加 Python 3.6 为系统执行的默认 Python
echo "alias pip='/usr/local/bin/pip'" >>/etc/profile.d/python.sh
                                                # 添加 pip 为系统执行的默认 pip
source /etc/profile                      # 使系统配置生效
pip install django==2.0 uwsgi -i https://pypi.tuna.tsinghua.edu.cn/simple
                                                # 安装 Django 和 uWSGI
```

## 2、创建测试 Django 项目 demonginx 及项目应用 Nginx

配置样例如下：

```

cd /opt/nginx-web/pythonweb
django-admin.py startproject demonginx
cd demonginx
sed -i "s/ALLOWED_HOSTS = \[.*/ALLOWED_HOSTS = \['\*', \]/g" demonginx/settings.py

# 创建项目应用 Nginx 及测试页面
django-admin.py startapp nginx

cat >>nginx/views.py<<EOF
from django.http import HttpResponse
def index(request):
    return HttpResponse("<h1>Hello Nginx for Django!</h1>")
EOF

sed -i "/\]/i\    path('',nginx_views.index,name=\"index\")," demonginx/urls.py
```

## 3、创建默认 admin 管理后台账号

配置样例如下：

python manage.py migrate
python manage.py createsuperuser --username admin --email admin@example.com

启动测试 Django 项目测试 Python 网站的有效性，测试成功后关闭该进程。

python manage.py runserver 0.0.0.0:9080

## 4、配置 uWSGI 服务器

配置样例如下：

```

cat>/opt/nginx-web/pythonweb/demonginx/nginx_uwsgi.ini<<EOF
[uwsgi]
socket = :9080
chdir        = /opt/nginx-web/pythonweb/demonginx  # 设置 Python 文件目录
module       = demonginx.wsgi                   # demonginx 项目的 wsgi.py 位置
master       = true                             # 主进程模式
processes    = 2                                # 开启两个工作进程
vacuum       = true                             # 退出时自动删除 UNIX socket 和 PID 文件
max-requests = 1000                             # 每个工作进程设置请求数为 1000
limit-as     = 512                              # 每个 uWSGI 工作进程的虚拟内存为 512MB
buffer-size  = 32768                            # uWSGI 接收数据包的缓存区大小为 32KB
pidfile = /var/run/uwsgi9080.pid                # 进程 pid 文件
daemonize = /opt/nginx-web/pythonweb/demonginx/uwsgi9080.log   
    # 使进程在后台运行，并输出日志到 uwsgi9080.log
EOF
```

## 5、配置 uWSGI 服务器守护进程 supervisord

配置样例如下：

```

## 启用 supervisord Web 管理
sed -i "s/^;\[inet_http/\[inet_http/g" /etc/supervisord.conf
sed -i "s/^;port/port/g" /etc/supervisord.conf

## 设置 supervisord
cat>/etc/supervisord.d/demonginx.ini<<EOF
# 配置进程运行命令
[program:demonginx]
command=/usr/local/bin/uwsgi --ini /opt/nginx-web/pythonweb/demonginx/nginx_uwsgi.ini
directory=/opt/nginx-web/pythonweb/demonginx  # 进程运行目录
startsecs=5                           # 启动 5 秒后没有异常则退出表示进程正常启动，默认为 1 秒
autostart=true                        # 在 supervisord 启动的时候也自动启动
autorestart=true                      # 程序退出后自动重启
EOF

# 启动 demonginx 的 uWSGI 服务
systemctl restart supervisord
```

## 6、Nginx 配置

Nginx 配置样例如下：

```

## Python 网站配置
server {
    listen         8083; 
    server_name    localhost
    charset UTF-8;

    client_max_body_size 75M;

    location / {
        include uwsgi_params;         # 引入 uWSGI 默认参数配置
        uwsgi_pass 127.0.0.1:9080;    # uWSGI 服务端口
        uwsgi_read_timeout 2;
    }
}

## supervisord Web 管理配置
server {
    listen         9083;
    server_name    localhost
    charset UTF-8;

    location / {
        allow 192.168.2.0/24;
        deny all;
        proxy_pass 127.0.0.1:9001;    # supervisord 服务端口
    }
}
```

## 7、启动 Nginx 服务

配置样例如下：

```

# 测试 Nginx 配置
nginx -t

# 重启 Nginx 服务
systemctl restart nginx
```