# Jenkins 安装与配置简述

> 原文：[`www.weixueyuan.net/a/837.html`](http://www.weixueyuan.net/a/837.html)

Jenkins 是基于 Java 开发的一个开源的持续集成项目，其提供了一个可扩展的可对代码持续集成、发布（代码编译、打包、部署）及交付的 Web 化操作平台。Jenkins 拥有超过 1000 个插件，使其支持包括 SVN、Git 等多种版本的管理工具（SCM）的代码管理，也可以快速实现 Java、Node.js、.Net 等语言项目的编译构建，并支持包括 Docker 在内的多种形式的部署交付。通过 Jenkins 的 Web 化管理界面，依赖各种强大的插件功能，可以使 Nginx 的配置变更管理变得更加便捷和安全。

使用 Jenkins 官方提供的 Docker 镜像，可以很方便地搭建 Jenkins 工作环境，搭建过程如下：

## 1) 初始化系统环境

主机的操作系统为 CentOS 7.6，初始化 Docker 环境如下：

```

yum install -y yum-utils                        # 安装 yum 工具
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo                               # 安装 Docker 安装源
yum install -y docker-ce docker-compose # 安装 Docker 和 docker-compose
systemctl enable docker                 # 将 Docker 注册为自启动服务
systemctl start docker
```

## 2) 编写 docker-compose 脚本

将脚本保存为 jenkinsci.yaml

```

jenkinsci:
    image: 'jenkinsci/blueocean'
    restart: always
    hostname: 'jenkinsci'
    container_name: jenkinsci
    environment:
        - PATH=/opt/apps/apache-maven-3.5.3/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
        - JAVA_OPTS="-Duser.timezone=Asia/Shanghai"
        - JENKINS_SLAVE_AGENT_PORT=50000
    ports:
        - '8086:8080'
        - '50000:50000'
```

## 3) 数据持久化

Jenkins 需要持久化的是 Jenkins 的运行目录，该目录包含其运行的所有配置文件，具体如下：

```

docker cp jenkinsci:/var/jenkins_home /opt/data/apps/jenkinsci/
chown -R 1000:1000 /opt/data/apps/jenkinsci/jenkins_home
echo "
    volumes:
        - '/opt/data/apps/jenkinsci/jenkins_home:/var/jenkins_home'
        - '/opt/data/apps/jenkinsci/apps:/opt/apps'
" >>jenkinsci.yaml

docker stop jenkinsci
docker rm jenkinsci
docker-compose -f jenkinsci.yaml up -d
```

## 4) 初始化配置及插件

Jenkins 启动后，在浏览器中访问 http://IP:8086 即可进入初始化安装界面，使用初始化密码登录，选择安装推荐插件即可。

通过以下指令获取初始化密码：

docker exec -it jenkinsci2 cat /var/jenkins_home/secrets/initialAdminPassword

Jenkins 是以任务（Job）为管理单元的，常用的任务类型有自由风格、Maven 项目、文件夹和流水线（pipeline）四种，本样例中仅使用自由风格任务类型。自由风格及流水线任务按照工作流程被划分为多个阶段，Jenkins 负责维护和管理任务在每个阶段的执行，并通过工作流的状态，按照任务的设定推动任务工作流的完成。自由风格任务的 6 个阶段配置如下表所示。

| 阶段配置名称 | 英文名称 | 阶段配置作用 |
| 全局配置 | General | 用以任务维护相关的配置，如时间戳格式、构建历史管理、构建参数等配置 |
| 代码仓库配置 | SCM | 用以配置代码仓库类型及代码仓库地址和账号 |
| 自动触发构建的条件配置 | Build Triggers | 用以设置自动触发当前任务的外部条件 |
| 构建环境配置 | Build Environment | 设置构建前的环境变量等配置 |
| 构建操作配置 | Build | 设置用以构建的工具或构建的脚本 |
| 构建后动作配置 | Post-build Actions | 设置构建后的相关动作，如生成构建报告、构建归档或触发其他构建等 |