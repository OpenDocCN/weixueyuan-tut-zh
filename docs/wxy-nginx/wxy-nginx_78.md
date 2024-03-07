# Nginx GitLab（配置归档工具）配置

> 原文：[`www.weixueyuan.net/a/831.html`](http://www.weixueyuan.net/a/831.html)

GitLab 是使用 Ruby 语言编写的 Git 仓库管理工具，以 Git 作为代码管理工具，并提供了 Web 管理、WIKI 及 Issue 等功能。GitLab 是按照 MIT 许可证分发的开源软件，已被很多知名公司使用，目前由 GitLabInc. 开发维护。

GitLab 可以搭建在私有服务器上，被授权的用户可以创建自己的代码仓库，并可授权给多人协作进行维护。GitLab 拥有与 GitHub 类似的功能，可以通过 Web 浏览器浏览代码、管理缺陷和注释。通过 GitLab 管理 Nginx 配置文件可以从 Web 浏览器中非常方便地浏览到提交过的历史变更，也可以利用 Git 相关命令实现 Nginx 配置的快速回滚操作。

GitLab 同样支持以 Docker 方式部署，官方在 Docker Hub 中也提供了可直接使用的镜像，通过编写相应的 docker-compose 脚本，可以快速搭建 GitLab 服务器，部署过程如下。

#### 1) 初始化系统环境

主机的操作系统为 CentOS 7.6，初始化 Docker 环境如下：

```

yum install -y yum-utils                        # 安装 yum 工具
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo                           　　 # 安装 Docker 安装源
yum install -y docker-ce docker-compose # 安装 Docker 和 docker-compose
systemctl enable docker                 # 将 Docker 注册为自启动服务
systemctl start docker
```

#### 2) 编写 docker-compose 脚本

创建 docker-compose 脚本，保存为 gitlab.yaml。

```

gitlab:
    image: 'gitlab/gitlab-ce:latest'
    restart: always
    hostname: 'gitlab'
    container_name: gitlab
#   environment:
#     GITLAB_OMNIBUS_CONFIG: |
      #external_url ‘https://gitlab.example.com’
      #Add any other gitlab.rb configuration here, each on its own line
    ports:
        - '8080:80'
        - '8443:443'
```

#### 3) 持久化 GitLab 数据

GitLab 需要持久化的有 3 个部分的内容，分别是 GitLab 的配置、GitLab 的代码仓库和 GitLab 日志。

```

# 运行 GitLab 容器
docker -f gitlab.yaml up -d

# 创建挂载目录并复制原容器内的文件
mkdir -p /opt/data/apps/gitlab
docker cp gitlab:/etc/gitlab /opt/data/apps/gitlab/config
docker cp gitlab:/var/opt/gitlab /opt/data/apps/gitlab/data
docker cp gitlab:/var/log/gitlab /opt/data/apps/gitlab/logs
chown -R 998:998 /opt/data/apps/gitlab/logs
# 添加挂载卷配置
echo "
    volumes:
        - '/opt/data/apps/gitlab/config:/etc/gitlab'
        - '/opt/data/apps/gitlab/logs:/var/log/gitlab'
        - '/opt/data/apps/gitlab/data:/var/opt/gitlab'
" >>gitlab.yaml
docker stop gitlab
docker rm gitlab
docker-compose -f gitlab.yaml up -d
```

GitLab 运行后可通过 http://IP:8080 访问登录。如果 GitLab 仅作为代码仓库应用，只需默认配置即可使用。